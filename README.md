### 1. Inserción del Componente

```html
<app-campo-lesion
  [(lesionesSeleccionadas)]="lesionesSeleccionadas"
  [deshabilitado]="deshabilitado"
></app-campo-lesion>
```

---

### 2. Definición del Componente

#### TypeScript (`.ts`)

```typescript
import { Component, Input } from '@angular/core';

export class CoordenadaLesion {
  constructor(public x: number, public y: number) {}
}

@Component({
  selector: 'app-campo-lesion',
  templateUrl: './campo-lesion.component.html',
  styleUrls: ['./campo-lesion.component.css']
})
export class CampoLesionComponent {
  @Input() deshabilitado = false;
  @Input() lesionesSeleccionadas: CoordenadaLesion[] = [];

  ngOnInit() {}

  createCircle(coordenada: CoordenadaLesion) {
    if (!this.lesionesSeleccionadas) {
      this.lesionesSeleccionadas = [];
    }
    this.lesionesSeleccionadas.push(coordenada);
  }

  removerLesion(coordenada: CoordenadaLesion) {
    if (this.deshabilitado) {
      return;
    }

    this.lesionesSeleccionadas = this.lesionesSeleccionadas.filter(
      (e) => !(e.x === coordenada.x && e.y === coordenada.y)
    );
  }

  seleccionarLesion(event: MouseEvent): void {
    if (this.deshabilitado) {
      return;
    }

    const { pageX, pageY, currentTarget }: any = event;
    const { left, top } = currentTarget.getBoundingClientRect();
    const { pageXOffset, pageYOffset } = window;
    const x = pageX - left - pageXOffset;
    const y = pageY - top - pageYOffset;

    const coordenada = new CoordenadaLesion(x, y);
    this.createCircle(coordenada);
  }
}
```

#### HTML (`.html`)

```html
<div class="noSelect" style="position: relative; width: 160px" id="pointer_div">
  <div class="lesiones"></div>
  <svg 
    (click)="seleccionarLesion($event)" 
    style="position: absolute; top: 0; width: 163px; height: 177px;" 
    id="lesiones" 
    xmlns="http://www.w3.org/2000/svg">
    
    <circle 
      *ngFor="let item of lesionesSeleccionadas" 
      (click)="removerLesion(item); $event.stopPropagation()" 
      style="cursor: pointer;" 
      [attr.cx]="item?.x" 
      [attr.cy]="item?.y" 
      r="5" 
      fill="red">
    </circle>
    
  </svg>
</div>
```

#### CSS (`.css`)

```css
.lesiones {
  content: url('~/assets/img/svg/lesiones.svg');
  width: 160px;
  display: block;
  margin: 0 auto 20px auto;
  -webkit-tap-highlight-color: transparent;
  outline: none;
}

.noSelect {
  -webkit-tap-highlight-color: transparent;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.noSelect:focus {
  outline: none !important;
}
```

---

### Explicación de los Componentes

1. **Inserción del Componente:**
   - Se inserta el componente `app-campo-lesion` en el componente padre, vinculando las propiedades `lesionesSeleccionadas` y `deshabilitado` mediante enlaces de datos.

2. **Definición del Componente:**
   - **TypeScript:**
     - Se define la clase `CoordenadaLesion` para representar las coordenadas `x` e `y` de una lesión.
     - El componente `CampoLesionComponent` maneja la lógica para agregar y remover lesiones, así como para manejar eventos de selección.
   
   - **HTML:**
     - Se utiliza un elemento `svg` para detectar clics y renderizar círculos (`<circle>`) en las posiciones seleccionadas.
     - Cada círculo es interactivo y permite remover una lesión al hacer clic sobre él.
   
   - **CSS:**
     - Se estilizan los elementos para evitar la selección de texto y mejorar la apariencia visual.
     - La clase `.lesiones` carga una imagen SVG y centra el contenido.

---