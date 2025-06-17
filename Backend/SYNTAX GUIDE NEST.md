## Interceptor

```typescript
import {NestInterceptor, ExecutionContext, CallHandler} from '@nest/common'

export class CustomInterceptor implements NestInterceptor {
intercept(ctx: ExecutionContext, next : CallHandler) : Observable<any> {
// code here
return next.handler() // returns observable
}
}

// To use this : 
@UseInterceptor(CustomInterceptor) // or pass multiple ones comma seperated, it will run in order
@Controller()
// can be used controller level, route level and global level

//main.ts
app.useGlobalInterceptor(new CustomInterceptor())

// To use dependecny injection method

// App.module.ts

providers: [{
	provide: APP_INTERCEPTOR
	useClass: CustomInterceptor
}]
```

## Guards
```typescript

import CanActivate from nest/common

export class AuthGuard implements CanActivate {
canActivate(ctx: ExecutionContext) : boolean | Promise<boolean> | Observable<boolean> {
	// guard logic here returning true or throwing error
}
}

// controller.ts
@UseGuards(AuthGuard) // can be used controller or route level

// Global level two methods
// app.ts

app.useGlobalGuards(new AuthGuard())

//app.module.ts
providers : [{
	provide: APP_GUARD,
	useClass: AuthGuard
}]
```

## Middleware

```typescript
export function UserMiddleware(req: Request, res: Response, next : NextFunction)
{
	// logic here
	req.user = user
	next()
}
```

```typescript
export class UserMiddleware implements NestMiddleware {
use(req: Request, res: Response, next : NextFunction){
	// logic here
	req.user = user
	next()
}
}
```

```typescript
export class AppModule implements NestModule {
	configure(consumer : MiddlewareConsumer){
		consumer.apply(middleware).forRoutes(...routes) 
		// class based and function both have same implementation here
		// * for global implementation
	}
}
```

## Pipes
```typescript
export class CustomPipe implements PipeTransform{
	constructor(...data){} // in order to pass any arguments
	transform(value : unknown, metadata : ArgumentMetadata){
		// logic here
		return newValue;
	}
}

function routeHandler(@Param('id', CustomPipe) id : number){} // if no args
function routeHandler(@Param('id', new CustomPipe(args)) id : number){}//if args
```

## Exception Filters

```typescript
@Catch(ExceptionType) // HttpException etc
export class CustomException implements ExceptionFilter {
	catch(exception : ExceptionType, ctx: ExecutionContext){
		// logic here to response to this exception
	}
}

// use case
@UseFilter(CustomException) // can be used on both route level and controller level

// Global level two methods
// app.ts

app.useGlobalFilters(new CustomException())

//app.module.ts
providers : [{
	provide: APP_EXCEPTION,
	useClass: CustomException
}]
```

## Decorators

```typescript

export const CustomDecorator = createParamsDecorator(
(data : unknown, ctx: ExecutionContext) => {
	// process here
	return data;
}
)
// controller.ts
routeHandler(@CustomDecorator(data) result : any){}
```

## Reflector Decorator

```typescript
// roles.ts
export const Roles = Reflector.createDecorator<string[]>()

// controller.ts
@Roles(['admin']) // can be class level or router level

// guard.ts

export class AuthGuard implements CanActivate {
constructor(private reflector : Reflector){}
canActivate(ctx: ExecutionContext) : boolean | Promise<boolean> | Observable<boolean> {
	const roleArray = this.reflector.get(Roles, ctx.getHandler() / ctx.getClass() ) // handler if used in route level else class if class level
	if(roleArray.includes(req.user.role)){} // guard logic here
}
```