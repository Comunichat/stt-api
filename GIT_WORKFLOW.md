# Git Workflow - Vibe STT Fork Management

## Configuración de Remotos

```bash
# Remoto origin: tu fork (Comunichat/stt-api)
origin  https://github.com/Comunichat/stt-api.git

# Remoto upstream: repositorio original (thewh1teagle/vibe)
upstream https://github.com/thewh1teagle/vibe.git
```

## Estructura de Ramas

- **`main`**: Rama principal del fork, siempre sincronizada con upstream/main
- **`feature/api-integration`**: Rama de desarrollo para modificaciones del CRM
- **`feature/*`**: Ramas para features específicos
- **`hotfix/*`**: Ramas para correcciones urgentes

## Workflow de Sincronización

### 1. Mantener fork actualizado

```bash
# Obtener últimos cambios del upstream
git fetch upstream

# Cambiar a main local
git checkout main

# Mergear cambios de upstream/main
git merge upstream/main

# Pushear cambios al fork
git push origin main
```

### 2. Trabajar en una feature

```bash
# Crear nueva rama desde main actualizado
git checkout main
git pull upstream main
git checkout -b feature/nueva-funcionalidad

# Hacer cambios y commits
git add .
git commit -m "feat: descripción del cambio"

# Pushear rama al fork
git push origin feature/nueva-funcionalidad
```

### 3. Sincronizar rama de feature con upstream

```bash
# Desde la rama de feature
git checkout feature/api-integration

# Obtener últimos cambios
git fetch upstream

# Rebase contra upstream/main para mantener historial limpio
git rebase upstream/main

# Si hay conflictos, resolverlos y continuar
# git add .
# git rebase --continue

# Force push si es necesario (solo en ramas de feature, nunca en main)
git push origin feature/api-integration --force-with-lease
```

### 4. Crear Pull Request

1. Desde GitHub, crear PR de `feature/api-integration` → `main` en tu fork
2. Una vez aprobado, mergear a main
3. Opcional: crear PR de tu fork → upstream si los cambios son beneficiosos para el proyecto original

## Comandos de Uso Frecuente

```bash
# Verificar estado del repositorio
git status
git log --oneline -10
git branch -a

# Sincronización rápida
git fetch upstream && git checkout main && git merge upstream/main && git push origin main

# Limpiar ramas mergeadas
git branch --merged main | grep -v "main" | xargs -n 1 git branch -d

# Ver diferencias con upstream
git log upstream/main..main --oneline
git log main..upstream/main --oneline
```

## Resolución de Conflictos

### En caso de conflictos durante merge/rebase:

1. **Ver archivos en conflicto**:
   ```bash
   git status
   ```

2. **Editar archivos manualmente** para resolver conflictos

3. **Marcar como resuelto**:
   ```bash
   git add archivo_resuelto.rs
   ```

4. **Continuar el proceso**:
   ```bash
   # Para merge
   git commit

   # Para rebase
   git rebase --continue
   ```

## Estrategia de Versionado

- **Tags del upstream**: Sincronizar tags importantes
  ```bash
  git fetch upstream --tags
  git push origin --tags
  ```

- **Releases propios**: Crear tags para versiones del fork
  ```bash
  git tag -a v1.0.0-crm -m "Version CRM integration"
  git push origin v1.0.0-crm
  ```

## Buenas Prácticas

1. **Siempre** trabajar en ramas de feature, nunca directamente en main
2. **Mantener main limpio** y sincronizado con upstream
3. **Commits atómicos** con mensajes descriptivos
4. **Rebase** en lugar de merge para mantener historial lineal
5. **Probar** antes de pushear cambios
6. **Documentar** cambios significativos en commits y PRs

## Convenciones de Commits

```
feat: nueva funcionalidad
fix: corrección de bug
docs: cambios en documentación
style: cambios de formato (no afectan la lógica)
refactor: refactorización de código
test: añadir o modificar tests
chore: tareas de mantenimiento
```

## Monitoreo de Cambios Upstream

- **Configurar notificaciones** en GitHub para el repo upstream
- **Revisar releases** periódicamente para nuevas funcionalidades
- **Evaluar impact** de cambios upstream en nuestras modificaciones

## Backup y Recuperación

```bash
# Backup de trabajo local
git stash push -m "trabajo en progreso"

# Crear backup de rama
git checkout -b feature/api-integration-backup

# Recuperar trabajo
git stash pop
```