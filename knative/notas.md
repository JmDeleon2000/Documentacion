# Instalar Knative local
## Versión actualizada
https://knative.dev/docs/getting-started/

# Pasos usados sep 2023 (WSL Ubuntu 22.04)
## Instalación de Knative
### Obtener binario de la página de releases
https://github.com/knative-extensions/kn-plugin-quickstart/releases

### Instalar
```{bash}
mv [NOMBRE-BINARIO] kn-quickstart
chmod +x kn-quickstart
sudo mv kn-quickstart /usr/local/bin
kn quickstart --install-eventing --install-serving --registry
```

### Verficación:
```{bash}
kind get clusters
```

## Instalación de API para crear funciones de Knative
### Obtener binario de la página de releases
https://github.com/knative/func/releases


### Instalar
```{bash}
mv [NOMBRE-BINARIO] func
chmod +x func
sudo mv func /usr/local/bin
```

### Verficación:
```{bash}
func version
```

# Uso de Knative functions
## Crear una función de Knative
```{bash}
func create -l <language> <function-name>
cd <function-name>
```


## Desplegar
```{bash}
func deploy --registry <registry>
```
# Eventing
TODO