---
title: Crie um Azure Red Hat OpenShift 4 cluster app restaurar usando Velero
description: Saiba como criar um restauro das suas aplicações de cluster Azure Red Hat OpenShift utilizando Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.custom: mvc
ms.openlocfilehash: 9eac34d643ba0df4be79a064858c580c884de727
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078566"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Crie um Azure Red Hat OpenShift 4 Cluster Application restaurar

Neste artigo, você vai preparar o seu ambiente para criar uma aplicação de cluster Azure Red Hat OpenShift 4. Vai aprender a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Criar um Azure Red Hat OpenShift 4 restaurar a aplicação

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Antes de começar

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Crie um Azure Red Hat OpenShift 4 backup de aplicações

Para criar uma cópia de segurança da aplicação Azure Red Hat OpenShift 4, consulte Criar uma cópia de [segurança Azure Red Hat OpenShift 4](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Restaurar uma aplicação Azure Red Hat OpenShift 4

Estes passos permitir-lhe-ão restaurar uma aplicação que foi previamente apoiada com Velero.
Pode verificar a lista de backups que são atualmente reconhecidos pelo cluster para ver que cópias de segurança estão disponíveis para restauro.  Para fazer este passo, terá de executar o seguinte comando:

_(Este passo pressupõe que instalou Velero num projeto chamado "velero")_

```bash
oc get backups -n velero
```

Assim que tiver a cópia de segurança que pretende restaurar, terá de efetuar o restauro com o seguinte comando:

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

Este passo irá criar os objetos Kubernetes que foram apoiados a partir do passo anterior ao criar uma cópia de segurança.

Para ver o estado da restauração, execute o seguinte passo:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando a fase `Completed` diz, a sua aplicação Azure Red Hat 4 deve ser restaurada.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>Restaurar uma aplicação Azure Red Hat OpenShift 4 com instantâneos incluídos


Para criar uma aplicação Azure Red Hat OpenShift 4 com volumes persistentes utilizando Velero, terá de realizar o restauro com o seguinte comando:

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
Este passo irá criar os objetos Kubernetes que foram apoiados a partir do passo anterior ao criar uma cópia de segurança.

Para ver o estado da restauração, execute o seguinte passo:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando a fase `Completed` diz, a sua aplicação Azure Red Hat 4 deve ser restaurada.

Para mais informações sobre como criar backups e restauros usando Velero consulte [backup Recursos OpenShift da forma nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, foi restaurada uma aplicação de cluster Azure Red Hat OpenShift 4. Aprendeu a:

> [!div class="checklist"]
> * Criar uma aplicação openShift v4 de cluster restaurar usando Velero
> * Crie uma aplicação openShift v4 de cluster restaurada com instantâneos usando Velero


Avance para o próximo artigo para saber sobre os recursos suportados Azure Red Hat OpenShift 4.

* [Azure Red Hat OpenShift v4 recursos suportados](supported-resources.md)


