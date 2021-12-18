# File Upload Feature using NestJS with Multer

https://tkssharma.com/file-upload-feature-using-nestjs/

# File Upload Feature using NestJS with Multer
By Tarun Sharma @tkssharma

June 27th, 2020 
nodejs development


<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo_text.svg" width="320" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## Description

[Nest](https://github.com/nestjs/nest) framework TypeScript starter repository.

## Installation

```bash
$ npm install
```

## Running the app

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Test

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```


## NestJS File Uploading Using Multer

### How to develop a simple file-uploading app

This guide will show you how you can implement file uploading into your NestJS application and the things you should keep in mind when doing so. You will develop an application with three endpoints that will do the following:

- Part-1 upload image to a server Directory
  - Upload an image;
  - Upload multiple images;
  - Get the image using the image path.
- part-2 process uploaded stream without storing
  - Upload an image;
  - Upload multiple images;
  - Get the image using the image path.
  - You will also add custom utilities to edit the file name and validate the file upload for images.

So without wasting any further time, let’s get into it.

## Setup - Part-1 upload image to a server Directory
The first thing you need to do is create a NestJS project that will hold our fileserver. For that, you need to open your terminal and run the following command:

```java
nest new nest-file-uploading && cd nest-file-uploading
```

This creates a new directory called nest-file-uploading and initializes it with a standard NestJS configuration.

With the directory in place, you can go ahead and install the needed dependencies for the application using the following commands:

```java
npm install @nestjs/platform-express --save
npm install @types/express -D
```

The last thing you need to do before jumping into coding is create the files and folders needed for the project. This is very simple because the application only needs one more file.

```java
mkdir src/utils
touch src/utils/file-uploading.utils.ts
```

To start the app, you can now execute npm run start:dev in your terminal.

## Uploading Files
Now that you have completed the setup process, you can go ahead and start implementing the actual functionality. Let’s start by importing the MulterModule in your AppModule so you can use Multer in your other files.

```java
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { MulterModule } from '@nestjs/platform-express';

@Module({
  imports: [MulterModule.register({
    dest: './files',
  })],
  controllers: [AppController],
})
export class AppModule {}
```

Here you import the MulterModule from @nestjs/platform-express and add it to your imports statement. You also define the destination in which the files will be saved when an upload occurs.

Note: This destination starts at the root path of the project, not the src folder.

The next step is to implement the actual uploading functionality, which is rather simple. You simply need to add a FileInterceptor() to a normal post request handler and then pull out the file from the request using the @UploadedFile() decorator.

```java
@Post()
@UseInterceptors(
	FileInterceptor('image'),
)
async uploadedFile(@UploadedFile() file) {
    const response = {
    	originalname: file.originalname,
    	filename: file.filename,
    };
    return response;
}
```

The FileInterceptor takes two arguments: a fieldName and an optional options object, which you will use later to check for the correct file types and give the file a custom name in the directory.

Uploading multiple files at once is almost the same; you just need to use the FilesInterceptor instead and pass an extra argument of the maximum number of files.

```java
@Post('multiple')
@UseInterceptors(
  FilesInterceptor('image', 20, {
    storage: diskStorage({
      destination: './files',
      filename: editFileName,
    }),
    fileFilter: imageFileFilter,
  }),
)
async uploadMultipleFiles(@UploadedFiles() files) {
  const response = [];
  files.forEach(file => {
    const fileReponse = {
      originalname: file.originalname,
      filename: file.filename,
    };
    response.push(fileReponse);
  });
  return response;
}
```

That was simple. The only problem here is that the user can upload every file type regardless of the file extension, which doesn’t suit all projects and that the file name is just some random number.

Let’s fix that by implementing some utilities in your file-upload.utils.ts file.

First, let’s implement a file type filter that only allows images to be uploaded.

```java
export const imageFileFilter = (req, file, callback) => {
  if (!file.originalname.match(/\.(jpg|jpeg|png|gif)$/)) {
    return callback(new Error('Only image files are allowed!'), false);
  }
  callback(null, true);
};

export const editFileName = (req, file, callback) => {
  const name = file.originalname.split('.')[0];
  const fileExtName = extname(file.originalname);
  const randomName = Array(4)
    .fill(null)
    .map(() => Math.round(Math.random() * 16).toString(16))
    .join('');
  callback(null, `${name}-${randomName}${fileExtName}`);
};
```

Here you create a middleware function, which checks if the file type is an image. If so it returns true, and the image will be uploaded. If not, you throw an error and return false for the callback.

The editFileName* *function has the same structure but creates a custom filename using the original name, the file extension, and four random numbers.

Now that you have created these two middleware functions, it’s time to use them in your app.controller.ts file. For that you just need to add an extra configuration object to the FileInterceptor, which then looks like this:

```java
@Post()
  @UseInterceptors(
    FileInterceptor('image', {
      storage: diskStorage({
        destination: './files',
        filename: editFileName,
      }),
      fileFilter: imageFileFilter,
    }),
  )
  async uploadedFile(@UploadedFile() file) {
    const response = {
      originalname: file.originalname,
      filename: file.filename,
    };
    return response;
  }

  @Post('multiple')
  @UseInterceptors(
    FilesInterceptor('image', 20, {
      storage: diskStorage({
        destination: './files',
        filename: editFileName,
      }),
      fileFilter: imageFileFilter,
    }),
  )
  async uploadMultipleFiles(@UploadedFiles() files) {
    const response = [];
    files.forEach(file => {
      const fileReponse = {
        originalname: file.originalname,
        filename: file.filename,
      };
      response.push(fileReponse);
    });
    return response;
  }
Lastly you will add a get route, which will take the image path as an argument and return the image using the sendFile method.

    @Get(':imgpath')
    seeUploadedFile(@Param('imgpath') image, @Res() res) {
      return res.sendFile(image, { root: './files' });
    }
```

## Testing the Application
Now that you have finished your application, it’s time to test it by sending an HTTP request to your endpoint. This can be done using the curl command in the terminal or by using an HTTP client software like Postman or Insomnia. I personally use Insomnia, but it should be almost the same in Postman.

Start the server using the following command:

```java
npm run start
```

As indicated by the terminal output, the server is now running on http://localhost:3000. To test the API you now only need to create a new HTTP request and change the request body to multipart so you can upload files.

## part-2 process uploaded stream without storing
In this process where we just need to upload and file and process on the fly then we can use this On this case we would not need mutler storage on server side so we don't need MulterModule.register anywhere in our code

Like we will just upload and convert uploaded file to stream and process it without uploading or storing it on server

Lets write controller router for this

```java
export const imageFileFilter = (req:any, file:any, callback:any) => {
  if (!file.originalname.match(/\.(csv)$/)) {
    return callback(new Error('Only csv files are allowed!'), false);
  }
  callback(null, true);
};


@Controller('/api/v1/test/upload')
@ApiBearerAuth('authorization')
@UsePipes(new ValidationPipe({
  whitelist: true,
  transform: true,
}))
export class UploadSupplierController {
  constructor(private readonly userSupplierProfileService: UserSupplierProfileService,
  ) { }

  @HttpCode(HttpStatus.OK)
  @ApiOkResponse({ description: RESULTS_RETURNED })
  @ApiBadRequestResponse({ description: PARAMETERS_FAILED_VALIDATION })
  @ApiTags('test')
  @ApiConsumes('multipart/form-data')
  @uploadFile('filename')
  @Post('/')
  @UseInterceptors(
    FileInterceptor('filename', {
      fileFilter: imageFileFilter,
    }),
  )
  async uploadedFile(@UploadedFile() file: any, @Query() params: UserSupplierParam, @User() userId: string, ) { 
    try {
      if (!file) {
        throw new BadRequestException('invalid file provided, allowed *.csv single file');
    }
      const readStream = getStream(file.buffer);
      await this.service.create(readStream, params.id, userId);
      return {success: true}
    }catch(err){
      throw err;
    }
  }
}
```

This controller can be used to upload single file or multiple files, NestJS provided @annotations to manage all these things, We just need to change uploadFile to uploadFiles.

```java
 @ApiConsumes('multipart/form-data')
  @uploadFiles('filename')
  @Post('/')
  @UseInterceptors(
    FileInterceptor('filename', {
      fileFilter: imageFileFilter,
    }),
  )
  async uploadedFiles(@UploadedFiles() file: any, @Query() params: UserSupplierParam, @User() userId: string, ) { 
    try {
      if (!file) {
        throw new BadRequestException('invalid file provided, allowed *.csv single file');
    }
      const readStream = getStream(file.buffer);
      await this.service.create(readStream, params.id, userId);
      return {success: true}
    }catch(err){
      throw err;
    }
  }
}
```

We can read uploaded single file stream or multiple file using buffer conversion to stream Now we can uploads file and if file is not valid then we can throw exception for bad request

```java
const getStream = require('into-stream');

  async uploadedFile(@UploadedFile() file: any, @Query() params: UserSupplierParam, @User() userId: string, ) { 
    try {
      if (!file) {
        throw new BadRequestException('invalid file provided, allowed *.csv single file');
    }
      const readStream = getStream(file.buffer);
      await this.service.create(readStream, params.id, userId);
      return {success: true}
    }catch(err){
      throw err;
    }
  }
```

This is all we need to setup file upload with or without server directory based upload, NestJS provided these cool @UploadFile annotations which really helps to manage things like mutler as its written on top of mutler, We can also pass mutler options while uploading

```java
  @Post()
  @UseInterceptors(
    FileInterceptor('image', {
      storage: diskStorage({
        destination: './files',
        filename: editFileName,
      }),
      fileFilter: imageFileFilter,
    }),
  )
```

If you have any questions or feedback, let me know.
