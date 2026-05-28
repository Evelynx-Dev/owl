# Registro de Módulos (Diseño Histórico)

> ⚠️ **Esta sección documenta el diseño original del sistema de registros,
> eliminado en Owl v0.10.0.** Se conserva como referencia histórica para
> una futura reimplementación. Ninguna funcionalidad aquí descrita está
> disponible en Owl v0.13.x.

---

## Historial

El sistema de registros fue parte de Owl v0.4.0–v0.9.0 e incluía:
- `modules/registry.mire` — sincronización con registro remoto via git
- `modules/download.mire` — descarga de tarballs con verificación SHA-256
- `modules/lock.mire` — archivo de lock con formato pipe-delimitado
- `modules/deps.mire` — instalación, actualización y eliminación de dependencias
- `modules/semver.mire` — resolución de versiones SemVer

En Owl v0.10.0 todo el sistema de dependencias externas fue eliminado para
centrarse en gestión local de proyectos. Los módulos fueron reemplazados por:
`diagnostics`, `fs_ops`, `profile`, `tests`, `toml`.

---

## Estado Actual (v0.13.x)

Owl es un gestor de proyectos local que proporciona:
- Creación de proyectos (`owl new`)
- Compilación y ejecución (`owl run`, `owl build`)
- Type-checking estático (`owl check`)
- Ejecución de tests (`owl test`)
- Perfiles de compilación (`owl profile`)
- Gestión de caché (`owl clean`)
- Información del proyecto (`owl info`)

Sin dependencias externas, sin acceso a red, sin registros.

---

## Diseño de Referencia (para futuro)

### Conceptos Generales

## Protocolo de Registro (Referencia)

### Conceptos Generales

| Concepto | Descripción |
|----------|-------------|
| **Registro** | URL que sirve un archivo `index.toml` con la lista de paquetes |
| **Paquete** | Tarball con código Mire + `owl.toml` |
| **Checksum** | SHA-256 del tarball completo |

### Formato del Índice

```toml
[registry]
name = "example-registry"
public_key = "ed25519:..."

[[package]]
name = "example"
version = "1.0.0"
url = "https://example.com/pkgs/example/v1.0.0.tar.gz"
checksum = "sha256:..."
dependencies = []
```

### Formato del Paquete

```
mymodule/
├── owl.toml           ← Metadatos del paquete
├── code/
│   └── lib.mire       ← Código fuente
└── tests/             ← Tests opcionales
```

---

## Notas de Implementación

- El sistema de registro requiere un cliente HTTP para descargas
- Se requiere validación de firmas Ed25519 para verificación de confianza
- El formato de paquetes usa tarball estándar con estructura definida
- La sintaxis de dependencias usa SemVer (^, ~, >=, etc.)