# Complete Angular Interview Questions & Answers Guide

## Angular Basics

### Q1: What is Angular and how does it differ from AngularJS?

**Answer:** Angular is a TypeScript-based web application framework developed by Google. Key differences from AngularJS:

- **Language**: Angular uses TypeScript, AngularJS uses JavaScript
- **Architecture**: Angular uses component-based architecture, AngularJS uses MVC
- **Mobile Support**: Angular has better mobile support
- **Performance**: Angular is faster with better change detection
- **CLI**: Angular has a powerful CLI tool
- **Structure**: Angular uses a hierarchical component tree

### Q2: What are the key features of Angular?

**Answer:**

- **Component-based architecture**
- **Two-way data binding**
- **Dependency injection**
- **Directives**
- **Services**
- **Routing**
- **Forms support**
- **HTTP client**
- **Testing utilities**
- **CLI tooling**

### Q3: What is TypeScript and why does Angular use it?

**Answer:** TypeScript is a superset of JavaScript that adds static typing. Angular uses TypeScript because:

- **Static typing** helps catch errors at compile time
- **Better IDE support** with autocomplete and refactoring
- **Object-oriented features** like classes, interfaces, and decorators
- **Better maintainability** for large applications
- **Transpiles to JavaScript** for browser compatibility

### Q4: What is the Angular CLI and what are its benefits?

**Answer:** Angular CLI is a command-line interface tool that helps create, build, and maintain Angular applications.

Benefits:

- **Project scaffolding**: `ng new project-name`
- **Component generation**: `ng generate component component-name`
- **Build optimization**: `ng build --prod`
- **Development server**: `ng serve`
- **Testing**: `ng test`, `ng e2e`
- **Linting**: `ng lint`
- **Best practices enforcement**

---

## Components & Templates

### Q5: What is a component in Angular?

**Answer:** A component is a TypeScript class decorated with `@Component` that controls a portion of the UI. It consists of:

```typescript
@Component({
  selector: 'app-example',
  templateUrl: './example.component.html',
  styleUrls: ['./example.component.css']
})
export class ExampleComponent {
  title = 'Hello World';
}
```

### Q6: What is the difference between a component and a directive?

**Answer:**

- **Component**: Has its own template and view, represents a UI element
- **Directive**: Modifies the behavior or appearance of existing elements

Types of directives:

- **Structural**: `*ngIf`, `*ngFor` (modify DOM structure)
- **Attribute**: `ngClass`, `ngStyle` (modify element attributes)
- **Component**: Components are directives with templates

### Q7: What is ViewEncapsulation in Angular?

**Answer:** ViewEncapsulation determines how component styles are applied:

```typescript
@Component({
  encapsulation: ViewEncapsulation.Emulated // default
})
```

Types:

- **Emulated**: Styles scoped to component (default)
- **ShadowDom**: Uses native Shadow DOM
- **None**: Global styles, no encapsulation

### Q8: What are lifecycle hooks in Angular?

**Answer:** Lifecycle hooks are methods that allow you to tap into key moments in a component's lifecycle:

```typescript
export class MyComponent implements OnInit, OnDestroy {
  ngOnInit() {
    // Component initialization
  }
  
  ngOnDestroy() {
    // Cleanup before component destruction
  }
}
```

---

## Data Binding

### Q9: What are the different types of data binding in Angular?

**Answer:**

1. **Interpolation**: `{{ expression }}`
2. **Property Binding**: `[property]="expression"`
3. **Event Binding**: `(event)="handler()"`
4. **Two-way Binding**: `[(ngModel)]="property"`

```html
<!-- Interpolation -->
<h1>{{ title }}</h1>

<!-- Property Binding -->
<img [src]="imageUrl">

<!-- Event Binding -->
<button (click)="onClick()">Click me</button>

<!-- Two-way Binding -->
<input [(ngModel)]="name">
```

### Q10: What is the difference between {{}} and [property]?

**Answer:**

- **Interpolation `{{}}`**: Converts expression to string and displays in template
- **Property Binding `[]`**: Binds component property to element property

```html
<!-- Both achieve the same result for strings -->
<div>{{ message }}</div>
<div [textContent]="message"></div>

<!-- Property binding is required for non-string values -->
<button [disabled]="isDisabled">Button</button>
```

---

## Directives

### Q11: What are structural directives?

**Answer:** Structural directives change the DOM structure by adding or removing elements.

Common structural directives:

```html
<!-- *ngIf -->
<div *ngIf="showDiv">This will show conditionally</div>

<!-- *ngFor -->
<li *ngFor="let item of items; let i = index">
  {{ i }}: {{ item }}
</li>

<!-- *ngSwitch -->
<div [ngSwitch]="value">
  <p *ngSwitchCase="'A'">Case A</p>
  <p *ngSwitchCase="'B'">Case B</p>
  <p *ngSwitchDefault>Default case</p>
</div>
```

### Q12: How do you create a custom directive?

**Answer:**

```typescript
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  constructor(private el: ElementRef, private renderer: Renderer2) {}
  
  @HostListener('mouseenter') onMouseEnter() {
    this.renderer.setStyle(this.el.nativeElement, 'backgroundColor', 'yellow');
  }
  
  @HostListener('mouseleave') onMouseLeave() {
    this.renderer.removeStyle(this.el.nativeElement, 'backgroundColor');
  }
}
```

Usage:

```html
<p appHighlight>Hover over me!</p>
```

---

## Services & Dependency Injection

### Q13: What is a service in Angular?

**Answer:** A service is a class that provides specific functionality and can be injected into components or other services.

```typescript
@Injectable({
  providedIn: 'root'
})
export class DataService {
  getData() {
    return ['item1', 'item2', 'item3'];
  }
}
```

### Q14: What is Dependency Injection?

**Answer:** Dependency Injection is a design pattern where dependencies are provided to a class rather than created within it.

```typescript
@Component({...})
export class MyComponent {
  constructor(private dataService: DataService) {
    // DataService is injected, not created
  }
}
```

Benefits:

- **Loose coupling**
- **Testability**
- **Reusability**
- **Maintainability**

### Q15: What are the different ways to provide services?

**Answer:**

1. **Root level** (application-wide singleton):

```typescript
@Injectable({
  providedIn: 'root'
})
```

2. **Module level**:

```typescript
@NgModule({
  providers: [MyService]
})
```

3. **Component level**:

```typescript
@Component({
  providers: [MyService]
})
```

---

## Routing

### Q16: How do you set up routing in Angular?

**Answer:**

1. **Define routes**:

```typescript
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserComponent },
  { path: '**', component: PageNotFoundComponent }
];
```

2. **Configure router module**:

```typescript
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

3. **Add router outlet**:

```html
<router-outlet></router-outlet>
```

### Q17: How do you navigate programmatically?

**Answer:**

```typescript
export class MyComponent {
  constructor(private router: Router) {}
  
  navigateToUser(id: number) {
    this.router.navigate(['/user', id]);
  }
  
  navigateWithQueryParams() {
    this.router.navigate(['/search'], { 
      queryParams: { q: 'angular' } 
    });
  }
}
```

### Q18: How do you get route parameters?

**Answer:**

```typescript
export class UserComponent implements OnInit {
  constructor(private route: ActivatedRoute) {}
  
  ngOnInit() {
    // Get route parameter
    const id = this.route.snapshot.paramMap.get('id');
    
    // Subscribe to parameter changes
    this.route.paramMap.subscribe(params => {
      const id = params.get('id');
    });
    
    // Get query parameters
    this.route.queryParams.subscribe(params => {
      const search = params['q'];
    });
  }
}
```

---

## Forms

### Q19: What are the different types of forms in Angular?

**Answer:**

1. **Template-driven forms**: Use directives in template
2. **Reactive forms**: Use FormControl, FormGroup in component

### Q20: How do you create a reactive form?

**Answer:**

```typescript
export class MyFormComponent {
  userForm = this.fb.group({
    name: ['', [Validators.required, Validators.minLength(2)]],
    email: ['', [Validators.required, Validators.email]],
    address: this.fb.group({
      street: [''],
      city: ['']
    })
  });
  
  constructor(private fb: FormBuilder) {}
  
  onSubmit() {
    if (this.userForm.valid) {
      console.log(this.userForm.value);
    }
  }
}
```

Template:

```html
<form [formGroup]="userForm" (ngSubmit)="onSubmit()">
  <input formControlName="name" placeholder="Name">
  <div *ngIf="userForm.get('name')?.invalid && userForm.get('name')?.touched">
    Name is required
  </div>
  
  <div formGroupName="address">
    <input formControlName="street" placeholder="Street">
    <input formControlName="city" placeholder="City">
  </div>
  
  <button type="submit" [disabled]="userForm.invalid">Submit</button>
</form>
```

### Q21: What is the difference between template-driven and reactive forms?

**Answer:**

|Template-driven|Reactive|
|---|---|
|Template-centric|Component-centric|
|Uses ngModel|Uses FormControl|
|Asynchronous|Synchronous|
|Less control|More control|
|Good for simple forms|Good for complex forms|

---

## HTTP Client & Observables

### Q22: How do you make HTTP requests in Angular?

**Answer:**

```typescript
@Injectable()
export class ApiService {
  constructor(private http: HttpClient) {}
  
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
  
  createUser(user: User): Observable<User> {
    return this.http.post<User>('/api/users', user);
  }
  
  updateUser(id: number, user: User): Observable<User> {
    return this.http.put<User>(`/api/users/${id}`, user);
  }
  
  deleteUser(id: number): Observable<void> {
    return this.http.delete<void>(`/api/users/${id}`);
  }
}
```

### Q23: What are Observables and how are they used in Angular?

**Answer:** Observables are a way to handle asynchronous data streams. They're used extensively in Angular for:

- **HTTP requests**
- **Event handling**
- **Route parameters**
- **Form changes**

```typescript
export class UserComponent implements OnInit {
  users$ = this.apiService.getUsers();
  
  ngOnInit() {
    // Subscribe to observable
    this.users$.subscribe(users => {
      console.log(users);
    });
    
    // Use async pipe in template (preferred)
    // <div *ngFor="let user of users$ | async">
  }
}
```

### Q24: What is the difference between Observable and Promise?

**Answer:**

|Observable|Promise|
|---|---|
|Can emit multiple values|Single value|
|Lazy (doesn't execute until subscribed)|Eager|
|Cancellable|Not cancellable|
|Rich operators (map, filter, etc.)|Limited chaining|
|Used in Angular|Native JavaScript|

---

## Lifecycle Hooks

### Q25: Explain all Angular lifecycle hooks in order:

**Answer:**

1. **ngOnChanges**: Called when input properties change
2. **ngOnInit**: Called after component initialization
3. **ngDoCheck**: Called during every change detection cycle
4. **ngAfterContentInit**: Called after content projection
5. **ngAfterContentChecked**: Called after content is checked
6. **ngAfterViewInit**: Called after view initialization
7. **ngAfterViewChecked**: Called after view is checked
8. **ngOnDestroy**: Called before component destruction

```typescript
export class LifecycleComponent implements OnInit, OnDestroy {
  ngOnInit() {
    console.log('Component initialized');
  }
  
  ngOnDestroy() {
    console.log('Component destroyed');
    // Cleanup subscriptions, timers, etc.
  }
}
```

---

## Pipes

### Q26: What are pipes and how do you use them?

**Answer:** Pipes transform data in templates:

```html
<!-- Built-in pipes -->
<p>{{ name | uppercase }}</p>
<p>{{ price | currency:'USD' }}</p>
<p>{{ today | date:'shortDate' }}</p>
<p>{{ items | json }}</p>
```

### Q27: How do you create a custom pipe?

**Answer:**

```typescript
@Pipe({
  name: 'reverse'
})
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    return value.split('').reverse().join('');
  }
}
```

Usage:

```html
<p>{{ 'hello' | reverse }}</p> <!-- outputs: olleh -->
```

---

## Modules

### Q28: What is an Angular module?

**Answer:** A module is a class decorated with `@NgModule` that organizes related components, directives, pipes, and services.

```typescript
@NgModule({
  declarations: [
    AppComponent,
    HeaderComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule
  ],
  providers: [
    ApiService
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Q29: What is lazy loading and how do you implement it?

**Answer:** Lazy loading loads modules only when needed, improving initial load time.

```typescript
// Routing with lazy loading
const routes: Routes = [
  {
    path: 'feature',
    loadChildren: () => import('./feature/feature.module').then(m => m.FeatureModule)
  }
];

// Feature module
@NgModule({
  declarations: [FeatureComponent],
  imports: [
    CommonModule,
    RouterModule.forChild([
      { path: '', component: FeatureComponent }
    ])
  ]
})
export class FeatureModule { }
```

---

## Testing

### Q30: How do you test Angular components?

**Answer:**

```typescript
describe('UserComponent', () => {
  let component: UserComponent;
  let fixture: ComponentFixture<UserComponent>;
  let mockApiService: jasmine.SpyObj<ApiService>;

  beforeEach(() => {
    const spy = jasmine.createSpyObj('ApiService', ['getUsers']);

    TestBed.configureTestingModule({
      declarations: [UserComponent],
      providers: [
        { provide: ApiService, useValue: spy }
      ]
    });

    fixture = TestBed.createComponent(UserComponent);
    component = fixture.componentInstance;
    mockApiService = TestBed.inject(ApiService) as jasmine.SpyObj<ApiService>;
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should load users on init', () => {
    const mockUsers = [{ id: 1, name: 'John' }];
    mockApiService.getUsers.and.returnValue(of(mockUsers));

    component.ngOnInit();

    expect(mockApiService.getUsers).toHaveBeenCalled();
  });
});
```

---

## Performance & Optimization

### Q31: What is OnPush change detection strategy?

**Answer:** OnPush strategy only checks component when:

- Input references change
- Event is triggered
- Observable emits

```typescript
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class OptimizedComponent {
  @Input() data: any;
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  updateData() {
    // Manually trigger change detection if needed
    this.cdr.markForCheck();
  }
}
```

### Q32: How do you optimize Angular applications?

**Answer:**

1. **Use OnPush change detection**
2. **Implement lazy loading**
3. *_Use TrackBy functions in _ngFor__
4. **Preload modules**
5. **Use async pipes**
6. **Bundle analysis and tree shaking**
7. **Service workers for caching**
8. **Use CDN for static assets**

```typescript
// TrackBy example
trackByFn(index: number, item: any) {
  return item.id;
}
```

```html
<div *ngFor="let item of items; trackBy: trackByFn">
  {{ item.name }}
</div>
```

---

## Advanced Topics

### Q33: What is content projection?

**Answer:** Content projection allows you to insert content into a component from its parent.

```typescript
// Child component
@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <div class="header">
        <ng-content select="[slot=header]"></ng-content>
      </div>
      <div class="body">
        <ng-content></ng-content>
      </div>
    </div>
  `
})
export class CardComponent { }
```

```html
<!-- Parent template -->
<app-card>
  <h2 slot="header">Card Title</h2>
  <p>Card content goes here</p>
</app-card>
```

### Q34: What are guards in Angular routing?

**Answer:** Guards control navigation to/from routes:

```typescript
@Injectable()
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}
  
  canActivate(): boolean {
    if (this.auth.isLoggedIn()) {
      return true;
    } else {
      this.router.navigate(['/login']);
      return false;
    }
  }
}
```

Types of guards:

- **CanActivate**: Can route be activated?
- **CanDeactivate**: Can user leave route?
- **CanLoad**: Can module be loaded?
- **Resolve**: Resolve data before navigation

### Q35: What is Angular Universal?

**Answer:** Angular Universal enables server-side rendering (SSR) for Angular applications.

Benefits:

- **Better SEO**
- **Faster initial load**
- **Better social media sharing**
- **Improved accessibility**

```bash
ng add @nguniversal/express-engine
npm run build:ssr
npm run serve:ssr
```

### Q36: What are some common Angular interview coding challenges?

**Answer:** Common coding challenges include:

1. **Create a search filter component**
2. **Implement a counter with increment/decrement**
3. **Build a todo list with CRUD operations**
4. **Create a custom validator**
5. **Implement a debounced search**
6. **Build a pagination component**

Example - Debounced search:

```typescript
export class SearchComponent implements OnInit {
  searchControl = new FormControl();
  results$ = this.searchControl.valueChanges.pipe(
    debounceTime(300),
    distinctUntilChanged(),
    switchMap(term => this.searchService.search(term))
  );
  
  ngOnInit() {
    this.results$.subscribe(results => {
      console.log(results);
    });
  }
}
```

---

## Final Tips for Angular Interviews

### Preparation Strategy:

1. **Understand core concepts** thoroughly
2. **Practice coding** on platforms like StackBlitz
3. **Build sample projects** to demonstrate skills
4. **Review Angular documentation** for latest features
5. **Practice explaining concepts** clearly
6. **Prepare questions** to ask the interviewer

### Common Topics to Review:

- **Component architecture and communication**
- **State management (NgRx if applicable)**
- **Performance optimization techniques**
- **Testing strategies**
- **Security best practices**
- **Angular ecosystem and tooling**

### Sample Projects to Build:

- **Todo application** with full CRUD
- **E-commerce product catalog** with filtering
- **User management system** with authentication
- **Real-time chat application** with WebSockets
- **Data dashboard** with charts and visualizations

Good luck with your Angular interview!