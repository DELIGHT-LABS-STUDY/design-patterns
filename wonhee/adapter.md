### Adapter 패턴

서로 다른 인터페이스를 가진 코드를 함께 동작하도록 하는 구조적 디자인 패턴
기존 코드를 변경하지 않고 새로운 인터페이스/라이브러리와 작동하게 해주는 래퍼(wrapper)

### 예시

Wallet 인터페이스를 구현한 CrossmarkWallet, GemWallet 클래스 (일종의 Adapter)

각 sdk 의 인터페이스가 다르기 때문에 Adapter를 통해 통일된 인터페이스를 제공

외부에서는 통일된 인터페이스에 의존하기 때문에 새로운 sdk 를 추가하거나 변경해도 외부 코드에 영향을 주지 않을 수 있음

```ts
import type { Action as InfoAction } from '../../store/walletInfo';
import type { MaybePromise } from '../types';

export enum WalletId {
  GemWallet = 'gem-wallet',
  Crossmark = 'crossmark',
  Xaman = 'xaman',
  WalletConnect = 'walletconnect',
}

export interface Wallet {
  readonly id: WalletId;
  connect: (onConnect: InfoAction['setInfo']) => Promise<void>;
  isConnected?: () => MaybePromise<boolean>;
  disconnect?: () => MaybePromise<void>;
  sendPayment: (payment: Payment) => Promise<string>;
}

export interface ExtensionWallet extends Wallet {
  isInstalled: () => MaybePromise<boolean>;
}
```

```ts
import { Action } from '../../store/walletInfo';
import { ExtensionWallet, Payment, WalletId } from './types';
import sdk from '@crossmarkio/sdk';

interface CrossmarkWalletInstalled {
  crossmark?: NonNullable<unknown>;
}

export class CrossmarkWallet implements ExtensionWallet {
  readonly id = WalletId.Crossmark;

  isInstalled() {
    return sdk.sync.isInstalled() ?? !!(window as unknown as CrossmarkWalletInstalled).crossmark;
  }

  async isConnected() {
    return await sdk.methods.connect(60 * 1000); // 60s
  }

  async connect(onConnect: Action['setInfo']) {
    if (!this.isInstalled()) {
      throw new Error('wallet not installed');
    }

    const { response } = await sdk.methods.signInAndWait();

    onConnect({
      address: response.data.address,
      network: response.data.network.type,
      walletId: this.id,
    });
  }

  async sendPayment(payment: Payment) {
    const address = sdk.methods.getAddress();

    if (!address) {
      throw new Error('wallet not connected');
    }

    const { response } = await sdk.methods.signAndSubmitAndWait({
      TransactionType: 'Payment',
      Account: address,
      Destination: payment.destination,
      Amount: payment.amount,
    });

    return response.data.resp.result.hash;
  }
}
```

```ts
import {
  getAddress,
  getNetwork,
  getPublicKey,
  isInstalled as isInstalledFn,
  sendPayment as sendPaymentFn,
} from '@gemwallet/api';

import { ExtensionWallet, Payment, WalletId } from './types';
import { Action as InfoAction } from '../../store/walletInfo';

interface GemWalletInstalled {
  gemWallet?: NonNullable<unknown>;
}

export class GemWallet implements ExtensionWallet {
  readonly id = WalletId.GemWallet;

  async isInstalled() {
    const res = await isInstalledFn();
    return res.result.isInstalled || !!(window as unknown as GemWalletInstalled).gemWallet;
  }

  async connect(onConnect: InfoAction['setInfo']) {
    if (!(await isInstalledFn())) {
      throw new Error('wallet not installed');
    }

    const res = await getPublicKey();

    if (res.result == null) {
      return;
    }

    const [addressRes, networkRes] = await Promise.all([getAddress(), getNetwork()]);

    const address = addressRes.result?.address;

    if (address == null) {
      throw new Error('address not found');
    }

    const network = networkRes.result?.network;

    if (network == null) {
      throw new Error('network not found');
    }

    onConnect({
      address,
      network,
      walletId: this.id,
    });
  }

  async sendPayment(payment: Payment) {
    if (!(await isInstalledFn())) {
      throw new Error('wallet not installed');
    }

    const res = await sendPaymentFn(payment);

    if (!res.result) {
      throw new Error('sendPayment failed');
    }

    return res.result.hash;
  }
}
```

위처럼 각 Wallet sdk 에 대한 구현은 Adapter 내부에만 존재하게되고,
외부에서는 Adapter의 인터페이스에만 의존 (의존 역전 원칙)

```ts
const connect = useCallback(
  async (wallet: Wallet) => {
    await wallet.connect(setInfo);
  },
  [setInfo]
);
```
