`nestjs`的学习笔记，不定时更新

### nest 中配置静态资源

- 在项目的根目录创建`public`文件夹

- 在`main.ts`中配置

  ```typescript
  /* 省略 */
  import { NestExpressApplication } from "@nestjs/platform-express";

  async function bootstrap() {
    const app = await NestFactory.create<NestExpressApplication>(AppModule); // 设置泛型
    app.useStaticAssets("public", {
      prefix: "/static/", // 设置虚拟目录
    }); // 设置静态资源项

    /* 省略 */
  }
  bootstrap();
  ```

- 在浏览器中访问：输入`http://localhost:8000/static/maps/1.jpg`，其中`/maps/1.jpg`是`public`下的资源。

### 文件上传

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

### 自定义装饰器

### 定时任务

### 异常过滤器
