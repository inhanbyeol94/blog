---
{"publish":true,"title":"config","created":"2025-07-19T02:45:03.355+09:00","modified":"2025-07-20T01:46:49.844+09:00","tags":["nestjs","env","component"],"cssclasses":""}
---

환경변수를 구성하기위해 필요한 패키지이며, Joi 패키지를 통해 환경변수 유효성 검증을 실행합니다.    
nestjs/config에 대해 자세한 설명은 공식문서를 참고하세요.  

<div data-auto-card-link-depth="-1" class="auto-card-link-container"><a href="https://docs.nestjs.com/techniques/configuration" class="auto-card-link-card" target="_blank" rel="noopener nofollow"><div class="auto-card-link-main"><div class="auto-card-link-title">Documentation | NestJS - A progressive Node.js framework</div><div class="auto-card-link-description">Nest is a framework for building efficient, scalable Node.js server-side applications. It uses progressive JavaScript, is built with TypeScript and combines elements of OOP (Object Oriented Programming), FP (Functional Programming), and FRP (Functional Reactive Programming).</div><div class="auto-card-link-host"><span>docs.nestjs.com</span></div></div><img draggable="false" src="https://nestjs.com/img/nest-og.png" class="auto-card-link-thumbnail" /></a></div>


## Install  
```zsh  
npm i --save @nestjs/config joi  
```  
  
  
## Configure  
`ConfigModule`을 제어할 `AppConfigModule`을 구성하고, 환경변수 값을 `AppConfig` 필드에 동적할당할 수 있도록 설정합니다. 추가로, `AppConfigModule`에서 검증할 수 있는 옵션을 static 변수로 구성이 필요합니다.  
  
> [!info]  
>  필드에 값을 할당하는 기준은 선언된 필드가 있는 경우에만 할당됩니다.    
>  `MIKRO_ORM_CLIENT_URL 값은 검증대상이나, 다른 객체에서 불필요한 값으로 필드를 생략할 수 있습니다`  
  
> [!tip] Tip  
> 환경변수의 형변환은 joi.object 옵션인 convert가 검증 타입에 따라 형변환을 진행합니다.    
> 추가로, boolean 검증 시 "true" | "false" 문자열 작성시에도 올바른 형변환을 지원합니다.  
  
### AppConfig
```ts
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import Joi from 'joi';

@Injectable()
export class AppConfig {
  constructor(private configService: ConfigService) {
    // 자신의 필드키를 배열로 변환
    const keys = Object.keys(this);

    // 환경변수 필드를 제외한 나머지 타입을 가진 필드는 제외
    const filter = keys.filter((key) => typeof this[key] !== 'object');

    // 필터된 필드키를 기준으로 자신의 명칭과 동일한 환경변수 값을 할당
    filter.forEach((key) => {
      this[key] = this.configService.get<unknown>(key);
    });
  }

  static readonly validationSchema = Joi.object({
    // App
    APP_NAME: Joi.string().required(),
    PORT: Joi.number().required(),
    TZ: Joi.string().required(),
    NODE_ENV: Joi.string().valid('local', 'dev', 'prod').default('local'),

    // MIKRO ORM
    MIKRO_ORM_CLIENT_URL: Joi.string().required(),
    MIKRO_ORM_TIMEZONE: Joi.string().required(),
    MIKRO_ORM_DB_NAME: Joi.string().required(),
    MIKRO_ORM_DEBUG: Joi.boolean(),

    // PRISMA ORM
    // DATABASE_URL: Joi.string().required(),
  }).options({ convert: true });

  readonly APP_NAME: string;
  readonly PORT: number;
  readonly TZ: string;
  readonly NODE_ENV: 'local' | 'dev' | 'prod';
}
```  
  
### AppConfigModule
```ts
import { Global, Module } from '@nestjs/common';
import { AppConfig } from './app-config';
import { ConfigModule } from '@nestjs/config';

@Global()
@Module({
  imports: [
    ConfigModule.forRoot({
      validationSchema: AppConfig.validationSchema,
    }),
  ],
  providers: [AppConfig],
  exports: [AppConfig],
})
export class AppConfigModule {}
```

<style>.auto-card-link-container{container-type:inline-size;position:relative;overflow:hidden;user-select:none;--auto-card-link-button-width:calc(var(--icon-size, 18px) + var(--size-2-3, 6px));--auto-card-link-indent-size:2.5em}&[data-auto-card-link-depth="1"]{margin-left:calc(var(--auto-card-link-indent-size) * 1)}&[data-auto-card-link-depth="2"]{margin-left:calc(var(--auto-card-link-indent-size) * 2)}&[data-auto-card-link-depth="3"]{margin-left:calc(var(--auto-card-link-indent-size) * 3)}&[data-auto-card-link-depth="4"]{margin-left:calc(var(--auto-card-link-indent-size) * 4)}&[data-auto-card-link-depth="5"]{margin-left:calc(var(--auto-card-link-indent-size) * 5)}&[data-auto-card-link-depth="6"]{margin-left:calc(var(--auto-card-link-indent-size) * 6)}&[data-auto-card-link-depth="7"]{margin-left:calc(var(--auto-card-link-indent-size) * 7)}.auto-card-link-title{white-space:normal!important;--lh:1.5em;line-height:var(--lh);height:calc(var(--lh) * 3);overflow:hidden;text-overflow:ellipsis}.auto-card-link-card{display:flex;flex-direction:row-reverse;height:8em;transition:20ms ease-in;cursor:pointer;text-decoration:none;color:var(--link-external-color,var(--highlight));background:var(--background-primary-alt,var(--darkgray));border:solid var(--border-width) var(--divider-color,var(--lightgray));border-radius:var(--radius-s,4px)}.auto-card-link-main{display:flex;flex-grow:1;flex-direction:column;justify-content:space-between;gap:.18em;padding:.5em .6em;overflow:hidden;text-align:left}.auto-card-link-description{overflow:hidden;--lh:1.4em;line-height:var(--lh);height:calc(var(--lh) * 3);color:var(--text-muted,var(--darkgray));font-size:var(--font-smallest, .9em)}.auto-card-link-host{font-size:var(--font-smallest, .9em);display:flex;flex-direction:row;align-items:center;text-overflow:ellipsis;white-space:nowrap}&:hover{color:var(--link-external-color-hover,var(--tertiary))}.auto-card-link-thumbnail{margin:0;width:unset!important;border-radius:var(--radius-s,4px) 0 0 var(--radius-s,4px)!important;height:100%;object-fit:cover;max-width:50%!important;pointer-events:none}.auto-card-link-container svg.external-icon{display: none}</style>
