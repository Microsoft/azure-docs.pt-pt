---
title: Adicione ou atualize o seu chapéu vermelho puxe segredo em um cluster Azure Red Hat OpenShift 4
description: Adicione ou atualize o seu chapéu vermelho puxe segredo sobre os clusters ARO existentes de 4.x
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 05/21/2020
keywords: puxar segredo, aro, openshift, chapéu vermelho
ms.openlocfilehash: b1a8e45a2eab1b7b4c8a5936049f404d5bd3441c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071091"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Adicione ou atualize o seu chapéu vermelho puxe segredo em um cluster Azure Red Hat OpenShift 4

Este guia cobre a adição ou atualização do seu segredo de puxar o chapéu vermelho para um cluster de 4.x do chapéu vermelho existente (ARO).

Se estiver a criar um cluster pela primeira vez, pode adicionar o seu segredo de força quando criar o seu cluster. Para obter mais informações sobre a criação de um cluster ARO com um segredo de puxar o chapéu vermelho, consulte [Criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Antes de começar

Este guia pressupõe que você tem um cluster Azure Red Hat OpenShift 4. Certifique-se de que tem acesso ao administrador ao seu cluster.

## <a name="prepare-your-pull-secret"></a>Prepare o seu segredo de puxar
Quando cria um cluster ARO sem adicionar um segredo de puxar o Chapéu Vermelho, um segredo de puxar ainda é criado automaticamente no seu cluster. No entanto, este segredo de puxar não está totalmente povoado.

Esta secção passa pela atualização que puxa o segredo com valores adicionais do seu segredo de puxar o chapéu vermelho.

1. Pegue o segredo nomeado `pull-secret` no espaço de `openshift-config` nomes e guarde-o para um ficheiro separado executando o seguinte comando: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    A sua saída deve ser semelhante à seguinte. (Note que o valor secreto real foi removido.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Vá ao seu [portal de clusters Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) e selecione **Download pull secret**. O teu segredo de puxar o Chapéu Vermelho vai parecer o seguinte. (Note que os valores secretos reais foram removidos.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Edite o ficheiro secreto que obteve do seu cluster adicionando as entradas encontradas no seu segredo de retirada do Chapéu Vermelho. 

    > [!IMPORTANT]
    > Incluir a `cloud.openshift.com` entrada do seu segredo de retirada do Chapéu Vermelho fará com que o seu cluster comece a enviar dados de telemetria para a Red Hat. Inclua esta secção apenas se pretender enviar dados de telemetria. Caso contrário, deixe a seguinte secção de fora.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Não remova ou altere a `arosvc.azurecr.io` sua entrada do seu segredo de mão. Esta secção é necessária para que o seu cluster funcione corretamente.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    O seu ficheiro final deve parecer o seguinte. (Note que os valores secretos reais foram removidos.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Certifique-se de que o ficheiro é válido JSON. Há muitas formas de validar o seu JSON. O exemplo a seguir utiliza jq:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Se houver um erro no ficheiro, parece que `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Adicione o seu segredo de puxar ao seu cluster

Executar o seguinte comando para atualizar o seu segredo de puxar.

> [!NOTE]
> Executar este comando fará com que os seus nós de cluster reiniciem um a um à medida que são atualizados. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Depois de definido o segredo, está pronto para ativar operadores certificados de chapéu vermelho.

### <a name="modify-the-configuration-files"></a>Modificar os ficheiros de configuração

Modifique os seguintes objetos para ativar os operadores de chapéus vermelhos.

Em primeiro lugar, modifique o ficheiro de configuração do Operador de Amostras. Em seguida, pode executar o seguinte comando para editar o ficheiro de configuração:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Alterar os `spec.architectures.managementState` valores e `status.architecture.managementState` valores de `Removed` `Managed` . 

O seguinte snippet YAML mostra apenas as secções relevantes do ficheiro YAML editado:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Em segundo lugar, executar o seguinte comando para editar o ficheiro de configuração do Operator Hub:  

```console
oc edit operatorhub cluster -o yaml
```

Altere os `Spec.Sources.Disabled` valores e `Status.Sources.Disabled` valores de `true` para todas `false` as fontes que desejar ativadas.

O seguinte snippet YAML mostra apenas as secções relevantes do ficheiro YAML editado:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Guarde o ficheiro para aplicar as suas edições.

## <a name="validate-that-your-secret-is-working"></a>Validar que o teu segredo está a funcionar

Depois de adicionar o seu segredo de puxar e modificar os ficheiros de configuração corretos, o seu cluster pode demorar vários minutos a ser atualizado. Para verificar se o seu cluster foi atualizado, execute o seguinte comando para mostrar as fontes de operadores certificados e operadores de chapéu vermelho disponíveis:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Se não vir os Operadores Certificados e Operadores de Chapéu Vermelho, aguarde alguns minutos e tente novamente.

Para garantir que o seu segredo de retirada foi atualizado e está a funcionar corretamente, abra o OperatorHub e verifique se existe qualquer Operador verificado pelo Chapéu Vermelho. Por exemplo, verifique se o Operador de Armazenamento de Contentores OpenShift está disponível e veja se tem permissões para instalar.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o Chapéu Vermelho, consulte [Usando segredos de imagem.](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)

Para saber mais sobre o Red Hat OpenShift 4, consulte a documentação da plataforma do [recipiente openshift do chapéu vermelho.](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
