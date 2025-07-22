---
{"publish":true,"title":"jwt","created":"2025-07-19T17:02:28.891+09:00","modified":"2025-07-21T22:59:01.564+09:00","tags":["nestjs","jwt","typescript","auth"],"cssclasses":""}
---

인증방식 중 제일 많이 쓰여지고있는 jwt 인증 방식을 구현하기위해 `jsonwebtoken` 패키지를 사용합니다.  

## Install
```bash
npm i jsonwebtoken
npm i -D @types/jsonwebtoken
```



## Configure

```bash
nest g mo jwt
nest g s jwt
```

### JwtModule
모듈을 글로벌 모듈로 등록하며, `JwtService`를 export합니다.
```ts
import { Global, Module } from '@nestjs/common';  
import { JwtService } from './jwt.service';  
  
@Global()  
@Module({  
  providers: [JwtService],  
  exports: [JwtService],  
})  
export class JwtModule {}
```


### JwtType
```ts
import { JwtPayload } from 'jsonwebtoken';

export interface SignPayload {
  id: number;
  role: number;
}

export interface TokenPayload extends SignPayload, JwtPayload {}
```

### JwtService

> [!tip] Tip  
> 필요한 환경변수를 활용하기 위해 [[nestjs/config\|AppConfig]]을 주입합니다.

```ts
import { Injectable } from '@nestjs/common';
import { decode, sign, verify } from 'jsonwebtoken';
import { TokenPayload, SignPayload } from './jwt.type';
import { AppConfig } from '../app-config/app-config';

@Injectable()
export class JwtService {
  constructor(private appConfig: AppConfig) {}

  sign(payload: SignPayload): string {
    return sign(payload, this.appConfig.JWT_SECRET_KEY, {
      expiresIn: this.appConfig.JWT_EXPIRES_IN,
    });
  }

  verify(token: string) {
    return verify(token, this.appConfig.JWT_SECRET_KEY) as TokenPayload;
  }

  decode(token: string) {
    return decode(token) as TokenPayload | null;
  }
}
```

### Test
```ts
import { Test, TestingModule } from '@nestjs/testing';
import { JwtService } from './jwt.service';
import { AppConfigModule } from '../app-config/app-config.module';
import { TokenPayload } from './jwt.type';
import { sign } from 'jsonwebtoken';
import { AppConfig } from '../app-config/app-config';

describe('JwtService', () => {
  let service: JwtService;
  let appConfig: AppConfig;

  const payload: TokenPayload = {
    id: 1,
    role: 1,
  };

  let token: string;
  let tokenOfBadSecretKey: string;
  let tokenOfExpired: string;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      imports: [AppConfigModule],
      providers: [JwtService],
    }).compile();

    service = module.get<JwtService>(JwtService);
    appConfig = module.get<AppConfig>(AppConfig);

    tokenOfBadSecretKey = sign({ id: 1, role: 1 }, 'bad-secret-key', {
      expiresIn: '1h',
    });

    tokenOfExpired = sign({ id: 1, role: 1 }, appConfig.JWT_SECRET_KEY, {
      expiresIn: -1,
    });
  });

  it('토큰 발행 (성공)', () => {
    const test = service.sign(payload);
    token = test;
    expect(test).toBeDefined();
  });

  it('토큰 검증 (성공)', () => {
    const test = service.verify(token);
    expect(test).toMatchObject(payload);
  });

  it('토큰 검증 (실패: 시크릿키가 다른 경우)', () => {
    expect(() => {
      service.verify(tokenOfBadSecretKey);
    }).toThrow();
  });

  it('토큰 검증 (실패: 만료된 경우)', () => {
    expect(() => {
      service.verify(tokenOfExpired);
    }).toThrow();
  });

  it('토큰 디코딩 (성공)', () => {
    const test = service.decode(token);
    expect(test).toMatchObject(payload);
  });

  it('토큰 디코딩 (실패: 잘못된 토큰이 전달된 경우)', () => {
    const test = service.decode('bad-token');
    expect(test).toBeNull();
  });
});
```

