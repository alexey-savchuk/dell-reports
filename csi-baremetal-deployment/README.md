## Савчук Алексей
## Отчет по стартовому заданию лаборатории Dell Technologies
## Установка csi-baremetal на kind

#### Параметры операционной системы
* Дистрибутив Linux — **Ubuntu 20.04**. Следует сказать, что при использовании другого дистрибутива возникали непредвиденные трудности.
* Размер выделенного дискового пространства — **50 ГБ**.

#### Необходимые зависимости
* [docker](https://docs.docker.com/engine/install/ubuntu/)
* [go](https://go.dev/dl/)
* [kubectl](https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/)
* [helm](https://helm.sh/docs/intro/install/)
* [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

#### Некоторые неочевидности, связанные с установкой *Go*
В качестве места для размещения файлов **go** выбрал домашнюю директорию пользователя, так как в этом случае при установке плагинов не требуются права суперпользователя.
```
# Версия go и целевая платформа могут отличаться
tar -C ~ -xzvf go1.18.linux-amd64.tar.gz
```
Для изменения переменной окружения `PATH` добавил в конец файла ~/.profile следующие несколько строк.
```
# set PATH so it includes user's go bin if it exists
if [ -d "$HOME/go/bin" ]; then
    PATH="$HOME/go/bin:$PATH"
fi

# set PATH so it includes user's go plugins bin if it exists
if [ -d "$HOME/go/bin/bin" ]; then
    PATH="$HOME/go/bin/bin:$PATH"
fi
```
После изменения файла ~/.profile перезашел в систему.

#### Получение исходного кода
```
git clone https://github.com/dell/csi-baremetal
git clone https://github.com/dell/csi-baremetal-operator
```

#### Сборка исходников и установка драйвера на кластер
Сборка исходников и установка на кластер драйвера csi-baremetal производил в соответствии с [руководством](https://github.com/dell/csi-baremetal/blob/master/docs/CONTRIBUTING.md).

#### Вид кластера после установки драйвера и валидации

```
user@pc:~/workdir$ kubectl get pods
NAME                                             READY   STATUS    RESTARTS   AGE
csi-baremetal-controller-595bd45d4f-x4j2q        4/4     Running   0          7m
csi-baremetal-node-controller-867f5bc4fb-wdmhm   1/1     Running   0          7m
csi-baremetal-node-kernel-5.4-7blnz              4/4     Running   0          7m
csi-baremetal-node-kernel-5.4-pr6wm              4/4     Running   0          7m
csi-baremetal-node-kernel-5.4-z8d7w              4/4     Running   0          7m
csi-baremetal-operator-5c68449f7b-vsqdl          1/1     Running   0          7m
csi-baremetal-se-patcher-7cbxt                   1/1     Running   0          7m
csi-baremetal-se-sx2tk                           1/1     Running   0          7m
web-0                                            1/1     Running   0          5m
web-1                                            1/1     Running   0          5m
web-2                                            1/1     Running   0          5m
user@pc:~/workdir$ kubectl get pvc
NAME         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS       AGE
data-web-0   Bound    pvc-6eae1ad5-1b2b-4129-ac1c-4e3eab7c4022   101Mi      RWO            csi-baremetal-sc   5m
data-web-1   Bound    pvc-5ce5b33c-73e5-43dc-9cea-725718ece0bc   101Mi      RWO            csi-baremetal-sc   5m
data-web-2   Bound    pvc-2cb056f0-57c9-4b53-be7d-6bc9ee2a88e2   101Mi      RWO            csi-baremetal-sc   5m
logs-web-0   Bound    pvc-f7f23c6c-9be2-40e1-ac98-3de92b7d643c   101Mi      RWO            csi-baremetal-sc   5m
logs-web-1   Bound    pvc-88cc9343-9f2e-4482-b377-a93c94166f24   101Mi      RWO            csi-baremetal-sc   5m
logs-web-2   Bound    pvc-90d5dcec-96ba-45dd-9d88-7e4cfcdd1b3a   101Mi      RWO            csi-baremetal-sc   5m
www-web-0    Bound    pvc-10ade2c3-78a5-4792-b499-5d27a18fbdbd   101Mi      RWO            csi-baremetal-sc   5m
www-web-1    Bound    pvc-b710b316-414a-425e-a8de-58668e8b4fd8   101Mi      RWO            csi-baremetal-sc   5m
www-web-2    Bound    pvc-b4c38204-df7d-4c7e-9752-0d0e38ac98ef   101Mi      RWO            csi-baremetal-sc   5m
```
