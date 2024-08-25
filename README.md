# Plantilla angular-dynamic-form-FormArray
Componente de formulario dinámico en Angular utilizando FormArray para manejar grupos de campos repetibles.

## Características

- **Formularios Reactivos**: Utiliza `FormBuilder` y `FormArray` para manejar formularios reactivos.
- **Campos Dinámicos**: Permite agregar y eliminar dinámicamente grupos de campos de formulario.
- **Validación**: Incluye validación básica de formularios.
- **Reutilizable**: Fácilmente adaptable para diferentes casos de uso.

## Casos de Uso

- **Registro de Usuarios**: Un formulario donde se pueden agregar múltiples usuarios, cada uno con su conjunto de campos (nombre, email, etc.).
- **Lista de Tareas**: Un formulario para agregar y gestionar una lista de tareas.
- **Inventario de Productos**: Un formulario para agregar productos a un inventario, con campos como nombre del producto, cantidad, precio, etc.
- **Encuestas o Cuestionarios**: Un formulario para crear encuestas donde cada pregunta puede tener múltiples opciones de respuesta.

## Instalación

1. Clona el repositorio:
   ```bash
   git clone https://github.com/tu-usuario/angular-dynamic-form-FormArray.git
   ```

2. Navega al directorio del proyecto:
   ```bash
   cd angular-dynamic-form-FormArray
   ```

3. Instala las dependencias:
   ```bash
   npm install
   ```

## Uso

1. Copia los archivos del componente (`user-form.component.html`, `user-form.component.ts`) a tu proyecto Angular.

2. Importa y declara el componente en tu módulo:
   ```typescript
   import { UserFormComponent } from './user-form/user-form.component';

   @NgModule({
     declarations: [
       UserFormComponent,
       // otros componentes
     ],
     // otros módulos
   })
   export class AppModule { }
   ```

3. Usa el componente en tu plantilla:
   ```html
   <app-user-form></app-user-form>
   ```

## Personalización

Para adaptar el componente a diferentes casos de uso, modifica los campos en la plantilla HTML y el método `createItemGroup` en el archivo TypeScript.

### Ejemplo: Formulario de Registro de Usuarios

#### Plantilla HTML (`user-form.component.html`)

```html
<form #f="ngForm" [formGroup]="form" novalidate>
  <div class="form-array" formArrayName="items">
    <ng-container *ngFor="let item of items.controls; let i = index" [formGroupName]="i" class="form-item">
      <div class="mb-4 p-4">
        <div class="d-flex justify-content-between align-items-center">
          <label class="form-label">
            Usuario <strong>Nro. {{ i + 1 }}</strong>
          </label>
          <div (click)="removeItem(i)" *ngIf="items.length > 1" class="remove-button" title="Eliminar usuario">
            <i class="ri-delete-bin-5-line"></i>
          </div>
        </div>

        <div class="form-group">
          <label class="form-label">Nombre</label>
          <input formControlName="nombre" class="form-control" required />
          <div class="invalid-feedback" *ngIf="f.form.get(['items', i, 'nombre'])?.invalid">
            Campo requerido
          </div>
        </div>

        <div class="form-group">
          <label class="form-label">Email</label>
          <input formControlName="email" class="form-control" required />
          <div class="invalid-feedback" *ngIf="f.form.get(['items', i, 'email'])?.invalid">
            Campo requerido
          </div>
        </div>
      </div>
    </ng-container>
  </div>
  <button type="button" (click)="addItem()" class="btn btn-primary">Agregar Usuario</button>
  <button type="submit" class="btn btn-success">Enviar</button>
</form>
```

#### Código TypeScript (`user-form.component.ts`)

```typescript
import {
  AfterViewInit,
  ChangeDetectorRef,
  Component,
  OnDestroy,
  OnInit,
  ViewChild,
} from '@angular/core';
import { FormArray, FormBuilder, FormGroup, NgForm } from '@angular/forms';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-user-form',
  templateUrl: './user-form.component.html',
  styleUrls: ['./user-form.component.scss'],
})
export class UserFormComponent implements OnInit, AfterViewInit, OnDestroy {
  @ViewChild('f') f!: NgForm;

  formEventSubscription$!: Subscription;
  form!: FormGroup;

  constructor(private fb: FormBuilder, private cd: ChangeDetectorRef) {}

  get items(): FormArray {
    return this.form.get('items') as FormArray;
  }

  ngOnInit(): void {
    this.form = this.fb.group({
      items: this.fb.array([]),
    });
    this.addItem(); // Agregar un grupo de campos al inicio
  }

  ngAfterViewInit(): void {
    // Aquí puedes agregar cualquier lógica adicional que necesites después de que la vista se haya inicializado
  }

  createItemGroup(): FormGroup {
    return this.fb.group({
      nombre: [null],
      email: [null],
    });
  }

  addItem(): void {
    this.items.push(this.createItemGroup());
    this.cd.detectChanges();
  }

  removeItem(index: number): void {
    this.items.removeAt(index);
    this.cd.detectChanges();
  }

  ngOnDestroy(): void {
    if (this.formEventSubscription$) {
      this.formEventSubscription$.unsubscribe();
    }
  }
}
```

# Explicación de las Directivas y Atributos

1. `<form #f="ngForm" [formGroup]="form" novalidate>`
   - **`#f="ngForm"`**: Esta directiva localiza el formulario en la plantilla y lo asigna a la variable `f`, que se puede usar para acceder a las propiedades y métodos del formulario en la plantilla.
   - **`[formGroup]="form"`**: Esta directiva vincula el formulario HTML al `FormGroup` definido en el componente TypeScript. `form` es el nombre de la propiedad del `FormGroup` en el componente.

2. `<div class="form-array" formArrayName="items">`
   - **`formArrayName="items"`**: Vincula el `FormArray` llamado `items` del `FormGroup` al contenedor. Esto permite iterar sobre los controles del `FormArray`.

3. `<ng-container *ngFor="let item of items.controls; let i = index" [formGroupName]="i" class="form-item">`
   - **`*ngFor="let item of items.controls; let i = index"`**: Itera sobre los controles del `FormArray` `items` y asigna cada control a `item` con su índice `i`.
   - **`[formGroupName]="i"`**: Vincula cada elemento del `FormArray` a un `FormGroup` específico basado en su índice `i`.

4. `<label class="form-label">Nombre</label>`
   - Etiqueta para el campo de entrada "Nombre".

5. `<input formControlName="nombre" class="form-control" required />`
   - **`formControlName="nombre"`**: Vincula el campo de entrada al control de formulario llamado `nombre` dentro del `FormGroup` actual.

6. `<div class="invalid-feedback" *ngIf="f.form.get(['items', i, 'nombre'])?.invalid">`
   - **`*ngIf="f.form.get(['items', i, 'nombre'])?.invalid"`**: Muestra el mensaje de error si el control de formulario `nombre` es inválido. `f.form.get(['items', i, 'nombre'])` accede al control `nombre` dentro del `FormArray` en el índice `i`.

La expresión `['items', i, 'nombre']` se usa para acceder a un control de formulario específico dentro de un `FormArray` anidado en un `FormGroup`. Aquí está el desglose de cada elemento en la expresión:

1. **`'items'`**:
   - Este es el nombre del `FormArray` dentro del `FormGroup` principal. El `FormArray` `items` contiene múltiples `FormGroup` o controles de formulario individuales.

2. **`i`**:
   - `i` es el índice del elemento actual en el `FormArray` `items`. Este índice se obtiene del bucle `*ngFor="let item of items.controls; let i = index"`, donde `i` representa la posición del `FormGroup` o control de formulario actual dentro del `FormArray`.

3. **`'nombre'`**:
   - Este es el nombre del control de formulario dentro del `FormGroup` en la posición `i` del `FormArray`. En este caso, es el control de formulario que maneja el campo de entrada para "Nombre".

## Contribuciones

¡Las contribuciones son bienvenidas! Si tienes alguna mejora o corrección, por favor abre un issue o envía un pull request.

## Licencia

Este proyecto está licenciado bajo la Licencia MIT - consulta el archivo [LICENSE](LICENSE) para más detalles.
