# NestJS
<p style="font-size:13px;">본 문서는 <a href="https://docs.nestjs.com">공식 문서</a>를 바탕으로 작성됨.</p> 

<ol>
  <li><a href="#Start">Start</a></li>
  <li><a href="#Controller">Controller</a></li>

</ol>


## Start
<a id="#Start"></a>

#### install Nest-cli
```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

<table>
  <thead>
    <tr>
      <th>nestJS</th>
      <th>ExpressJS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><span class="item">app.controller.ts</td>
      <td>MVC에서의 컨트롤러</td>
    </tr>
    <tr>
      <td><span class="item">app.controller.spec.ts</span></td>
      <td>컨트롤러 Unit test 코드</td>
    </tr>
    <tr>
      <td><span class="item">app.module.ts</td>
      <td>앱 최상단 모듈</td>
    </tr>
    <tr>
      <td><span class="item">app.service.ts</td>
      <td>MVC에서의 서비스</td>
    </tr>
    <tr>
      <td><span class="item">main.ts</td>
      <td>앱의 진입점</td>
    </tr>
  </tbody>
</table>


#### app.module.ts
```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { CatsController } from './cats.controller';
import { AdminController, TestController } from './test.controller';

@Module({
  imports: [],
  controllers: [AppController, CatsController, TestController, AdminController], //Some controllers
  providers: [AppService], //Some Providers
})
export class AppModule {}

```

#### main.ts
```typescript
import { NestFactory } from '@nestjs/core';
import { NestExpressApplication } from '@nestjs/platform-express';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  await app.listen(3000);
}
bootstrap();

```

## Controller
<a id="#Control"></a>


### 구조
```typescript
@Controller('some') //  routing '/some'
export class Controller {
  @Get() // @Get @Post @Delete @Put 
  someFunction() {
    return `request to '/some' with Get Method`;
  }

  @Post()
  someFunction() {
    return `request to '/some' with Post Method`;
  }

  @Get('a')
  someFunction() {
    return `request to '/some/a with Get Method'`
  }
}
```
Spring 구조랑 유사


### HttpCode
<p>Standard 방식 <strong style="color: red;">recommended</strong></p>

``` typescript
  @Get()
  @HttpCode(200) //Here
  findAll(@Req() request: Request: string{
    return `request data ${request.body}`;
  }
```
<p>Library-specific 방식 (expressjs)</p>

``` typescript
  @Get()
  findAll(@Res() response: Response, @Req() request: Request): string{
    response.status(200).send(`request data ${request.body}`);
    return;
  }
```
<table>
  <thead>
    <tr>
      <th>nestJS</th>
      <th>ExpressJS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>@Request(), @Req()</td>
      <td>req</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Response(), @Res()*</span></td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">res</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Next()</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">next</td>
    </tr><tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Session()</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.session</td>
    </tr><tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Param(key?: string)</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.params / req.params[key]</td>
    </tr><tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Body(key?: string)</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.body / req.body[key]</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Query(key?: string)</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.query / req.query[key]</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Headers(name?: string)</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.headers / req.headers[name]</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@Ip()</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.ip</td>
    </tr>
    <tr>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">@HostParam()</td>
      <td><span style="padding: 2px 6px; background-color: #f0f2f3;color: #2876d2;">req.hosts</td>
    </tr>
  </tbody>
</table>

### Header
``` typescript
  @Post
  @Header('Cache-Control', none) //Cache-Control <- none
  someFunction() {
    return 'set cache-control';
  }
```

### Redirect
``` typescript
  @Get()
  @Redirect('https://www.github.com', 302) //함수가 끝나면 redirect
  //301은 영구적, 302는 일시적

  @Get('repository')
  @Redirect('https://www.github.com', 302)
  RepositoryFunction(@Query('id') id:string) {
    if(id === "minsoo0715") {
      return {url: 'https://www.github.com/minsoo0715"}; //함수 안에서 redirect target을 바꿈
    }
  } 
```

### Param
``` typescript
  @Get(':id')
  someFunction(@Param('id') id: string):string {
    return `id Param is ${id}`
  }
```

### async
``` typescript
  @Get()
  async findAll(): Promise<any[]> {
    return [];
  }

  @Get()
  findAll(): Observable<any[]> {
    return of([]);
  }
  ```

  ### Dto

  ```typescript
  export class CreateUserDto() {
    name: string;
    age: string;
    id: string;

    //....getter or setter.. etc..
  }
```
```typescript
  @Post create(@Body() createUserDto: CreateUserDto) {
    return 'this acction adds new user';
  }
```
### 현재 코드 (Controller)
```typescript
import { Controller, Get, Query, Post, Body, Put, Param, Delete } from '@nestjs/common';
import { CreateCatDto, UpdateCatDto, ListAllEntities } from './dto';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(@Query() query: ListAllEntities) {
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return `This action removes a #${id} cat`;
  }
}
```  

## Provider(Service)

MVC 구조에서의 Service

### 현재 코드
<strong>cats.service.ts</strong>
``` typescript
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable() //제공자(Provider) 클래스에 붙이는 어노테이션
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```
<strong>cats.controller.ts</strong>

``` typescript
  @Post()
  async create(@Body() createCatDto: CreateCatDto) { //비동기
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll() {   //비동기
    return this.catsService.findAll();
  }
```

###

Injectable()이 있어야 의존성 주입 가능

``` typescript
@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {} //생성자 주입
  ~ ~ ~
```

## Middleware
  ### Controller로 진입하기 전 공통으로 실행되는 것
  
<strong>logger.middleware.ts</strong>
  ``` typescript
  import { Injectable, NestMiddleware } from '@nestjs/common';
  import { Request, Response, NextFunction } from 'express';

  @Injectable()
  export class LoggerMiddleware implements NestMiddleware {
    use(req: Request, res: Response, next: NextFunction) {
      console.log('Request');
      next();
    }
  }
  ```

  <strong>app.module.ts</strong>

  ``` typescript
  import { Module } from '@nestjs/common';
  import { AppController } from './app.controller';
  import { AppService } from './app.service';
  import { CatsController } from './cats.controller';
  import { CatsService } from './cats.service';
  import { AdminController, TestController } from './test.controller';
  import {LoggerMiddleware} from './logger.middleware.ts'

  @Module({
    imports: [],
    controllers: [AppController, CatsController, TestController, AdminController],
    providers: [AppService, CatsService],
  })

  export class AppModule implements NestModule {
    configure(consumer: MiddlewareConsumer) {
      consumer.apply(LoggerMiddleware).forRoutes('cats')
      //It excute when request to '/cats' route
      //consumer.apply(LoggerMiddleware.forRoutes({path: 'cats' method:RequestMethod.Get})) 
    }
  }
```
  
``` typescript
forRoutes({path: 'ab*cd', method: RequestMethod.ALL});
  //It can use wildcards. 
  ```
## TESTING

``` typescript
import { Test, TestingModule } from '@nestjs/testing'; 
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { AppModule } from './../src/app.module';

describe('AppController (e2e)', () => { //테스트 단위
  let app: INestApplication; //가상 앱

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile(); //AppModule 설정 + 컴파일

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  it('/ (GET)', () => {  //테스트 소 단위
    return request(app.getHttpServer()) //app의 http 서버에 요청 
      .get('/') //  루트 라우터(경로 설정) post('/')도 동작
      .expect(200) //상태코드 200 예상
      .expect('Hello World!'); //리턴 데이터 예상
  });
});
```

## SQL (typeorm)

### app.module.ts

``` bash
$ $ npm install --save @nestjs/typeorm typeorm mysql2
```
``` typescript
@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql', //db type
      host: 'localhost',
      port: 3306,
      username: 'root', 
      password: sqlpwd,
      database: 'test', // db name
      entities: [User], //entity for use
      synchronize: true, //synchronize(when there is no table, then it make automatically)
    }),
    UsersModule,
  ],

  controllers: [AppController, CatsController, TestController, AdminController],
  providers: [AppService, CatsService],
})
~ ~ ~
```
### ./users/user.module.ts
``` typescript
  @Module({
  imports: [TypeOrmModule.forFeature([User/*entity*/])],
  controllers: [UsersController],
  providers: [UsersService],
})
```

### ./users/users.service.ts
``` typescript
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm'; //
import { Repository } from 'typeorm'; //repository object
import { User } from './users.entitiy'; //entity

@Injectable()
export class UsersService { 
  constructor(
    @InjectRepository(User) //for use User entity in this service
    private userRepository: Repository<User>, //Repository Pattern
  ) {}

  async insert(user: User): Promise<any> {
    return this.userRepository.insert(user); // Repository< >.insert -> insert data
  }

  findAll(): Promise<User[]> {
    return this.userRepository.find(); //Repository< >.find -> find all data
  }

  findOne(id: string): Promise<User> {
    return this.userRepository.findOne(id); //Repository< >.findOne(id) -> findById
  }

  async remove(id: string): Promise<void> {
    await this.userRepository.delete(id); //Repository< >.delete(id) -> deleteById
  }
}
```
### ./users/users.controller.ts
``` typescript
~ ~ ~
@Post('register')
  async register(@Body() user: User) {
    await this.usersService.insert(user);
  }

  @Get('getuser')
  async getuser(@Param() params) {
    return await this.usersService.findOne(params.id);
  }

  @Get('getAlluser')
  async getAlluser() {
    return await this.usersService.findAll();
  }
~ ~ ~
```







