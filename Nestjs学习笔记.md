`nestjs`的学习笔记，不定时更新

[TOC]

## nest 中常用配置

### 配置静态资源

- 在项目的根目录创建`public`文件夹

- 在`main.ts`中配置

  ```typescript
  /* 略 */
  import { NestExpressApplication } from "@nestjs/platform-express";

  async function bootstrap() {
    const app = await NestFactory.create<NestExpressApplication>(AppModule); // 设置泛型
    app.useStaticAssets("public", {
      prefix: "/static/", // 设置虚拟目录
    }); // 设置静态资源项

    /* 略 */
  }
  bootstrap();
  ```

- 在浏览器中访问：输入`http://localhost:8000/static/maps/1.jpg`，其中`/maps/1.jpg`是`public`下的资源。

### 配置模板引擎

- `npm i ejs`，安装 ejs 模板引擎

- 在根目录创建`views`文件夹

- 然后在`main.ts`中配置

  ```typescript
  /* 略 */
  import { NestExpressApplication } from "@nestjs/platform-express";

  async function bootstrap() {
    const app = await NestFactory.create<NestExpressApplication>(AppModule); // 设置泛型
    //配置模板引擎
    app.setBaseViewsDir("views");
    app.setViewEngine("ejs");

    /* 略 */
  }
  bootstrap();
  ```

- 在对应控制器中配置：

  ```typescript
  @Controller("index")
  export class MainController {
    @Get()
    @Render("index") // 该路由会渲染views文件夹下index.ejs模板文件
    index() {
      return {};
    }
  }
  ```

### 配置 cookie、session

- `npm i cookie-parser`，`npm i -D @types/cookie-parser`，`npm i express-session`，`npm i -D @types/express-session` 安装依赖；

- 然后在`main.ts`中配置

  ```typescript
  /* 略 */
  import * as cookieParser from "cookie-parser";
  import * as session from "express-session";

  async function bootstrap() {
    const app = await NestFactory.create<NestExpressApplication>(AppModule);

    //配置cookie中间件
    app.use(cookieParser("this signed cookies"));

    //配置session的中间件
    app.use(
      session({
        secret: "keyboard cat",
        resave: true,
        saveUninitialized: true,
        cookie: { maxAge: 219000, httpOnly: true },
        rolling: true,
      })
    );
    await app.listen(3000);
  }
  bootstrap();
  ```

## 文件上传

- 首先引入相关的依赖 `UseInterceptors`，`UploadedFile`，`FileInterceptor`：

  ```tsx
  import { UseInterceptors, UploadedFile } from "@nestjs/common";
  import { FileInterceptor } from "@nestjs/platform-express";
  ```

- 在控制器中进行相关的配置：

  ```typescript
  import { createWriteStream } from 'fs'
  import { join } from 'path';

  /* 略 */
  @Post('doAdd')
  @UseInterceptors(FileInterceptor('file')) // 使用`UseInterceptors`装饰器,其中file是前端选择文件按钮的name属性
  doAdd(@Body() body, @UploadedFile() file) {
      const cw = createWriteStream(join(__dirname, '../../public', `${file.originalname}`)) // 创建文档写入流
      cw.write(file.buffer) //写入文件
      return 'success'
  }
  ```

## 自定义装饰器

## 定时任务

## 异常过滤器
