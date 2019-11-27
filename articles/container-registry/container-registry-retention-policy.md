---
title: Política para reter manifestos não marcados
description: Saiba como habilitar uma política de retenção no registro de contêiner do Azure para exclusão automática de manifestos não marcados após um período definido.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454809"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Definir uma política de retenção para manifestos não marcados

O registro de contêiner do Azure oferece a opção de definir uma *política de retenção* para manifestos de imagem armazenados que não têm marcas associadas (*manifestos não marcados*). Quando uma política de retenção é habilitada, os manifestos não marcados no registro são automaticamente excluídos após um número de dias que você definir. Esse recurso impede que o registro se preencha com artefatos que não são necessários e ajuda você a economizar em custos de armazenamento. Se o atributo `delete-enabled` de um manifesto não marcado for definido como `false`, o manifesto não poderá ser excluído e a política de retenção não se aplicará.

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos de comando neste artigo. Se você quiser usá-lo localmente, a versão 2.0.74 ou posterior será necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

> [!IMPORTANT]
> Este recurso está atualmente em visualização e algumas [limitações se aplicam](#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

> [!WARNING]
> Definir uma política de retenção com cuidado-os dados da imagem excluída são irrecuperáveis. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve definir uma política de retenção para manifestos não marcados. A exclusão de imagens não marcadas impedirá que esses sistemas extraiam as imagens do registro. Em vez de efetuar pull por manifesto, considere a adoção de um esquema de *marcação exclusivo* , uma [prática](container-registry-image-tag-version.md)recomendada.

## <a name="preview-limitations"></a>Limitações de visualização

* Somente um registro de contêiner **Premium** pode ser configurado com uma política de retenção. Para obter informações sobre as camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).
* Você só pode definir uma política de retenção para manifestos não marcados.
* A política de retenção atualmente se aplica apenas a manifestos que não são marcados *após* a política ser habilitada. Os manifestos não marcados existentes no registro não estão sujeitos à política. Para excluir os manifestos não marcados existentes, consulte exemplos em [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Sobre a política de retenção

O registro de contêiner do Azure faz referência à contagem de manifestos no registro. Quando um manifesto é desmarcado, ele verifica a política de retenção. Se uma política de retenção estiver habilitada, uma operação de exclusão de manifesto será enfileirada, com uma data específica, de acordo com o número de dias definido na política.

Um trabalho de gerenciamento de filas separado processa mensagens constantemente, dimensionando conforme necessário. Por exemplo, suponhamos que você não marcou dois manifestos, 1 hora, em um registro com uma política de retenção de 30 dias. Duas mensagens seriam enfileiradas. Em seguida, 30 dias depois, aproximadamente 1 hora, as mensagens seriam recuperadas da fila e processadas, supondo que a política ainda estava em vigor.

## <a name="set-a-retention-policy---cli"></a>Definir uma política de retenção-CLI

O exemplo a seguir mostra como usar o CLI do Azure para definir uma política de retenção para manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilitar uma política de retenção

Por padrão, nenhuma política de retenção é definida em um registro de contêiner. Para definir ou atualizar uma política de retenção, execute o comando [AZ ACR config Retention Update][az-acr-config-retention-update] no CLI do Azure. Você pode especificar um número de dias entre 0 e 365 para manter os manifestos não marcados. Se você não especificar um número de dias, o comando definirá um padrão de 7 dias. Após o período de retenção, todos os manifestos não marcados no registro são excluídos automaticamente.

O exemplo a seguir define uma política de retenção de 30 dias para manifestos não marcados no registro *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

O exemplo a seguir define uma política para excluir qualquer manifesto no registro assim que ele não estiver marcado. Crie essa política definindo um período de retenção de 0 dias. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validar uma política de retenção

Se você habilitar a política anterior com um período de retenção de 0 dias, poderá verificar rapidamente se os manifestos não marcados são excluídos:

1. Envie uma imagem de teste `hello-world:latest` imagem para o registro ou substitua outra imagem de teste de sua escolha.
1. Desmarcando a imagem de `hello-world:latest`, por exemplo, usando o comando [AZ ACR Repository][az-acr-repository-untag] redelete. O manifesto não marcado permanece no registro.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Em alguns segundos, o manifesto não marcado é excluído. Você pode verificar a exclusão listando manifestos no repositório, por exemplo, usando o comando [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests] . Se a imagem de teste foi a única no repositório, o repositório em si será excluído.

### <a name="disable-a-retention-policy"></a>Desabilitar uma política de retenção

Para ver a política de retenção definida em um registro, execute o comando [AZ ACR config Retention show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --registry myregistry
```

Para desabilitar uma política de retenção em um registro, execute o comando [AZ ACR config Retention Update][az-acr-config-retention-update] e defina `--status disabled`:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Definir uma política de retenção-Portal

Você também pode definir a política de retenção de um registro no [portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como usar o portal para definir uma política de retenção para manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilitar uma política de retenção

1. Navegue até o registro de contêiner do Azure. Em **políticas**, selecione **retenção** (versão prévia).
1. Em **status**, selecione **habilitado**.
1. Selecione um número de dias entre 0 e 365 para manter os manifestos não marcados. Selecione **Guardar**.

![Habilitar uma política de retenção no portal do Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Desabilitar uma política de retenção

1. Navegue até o registro de contêiner do Azure. Em **políticas**, selecione **retenção** (versão prévia).
1. Em **status**, selecione **desabilitado**. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre as opções para [Excluir imagens e repositórios](container-registry-delete.md) no registro de contêiner do Azure

* Saiba como [limpar automaticamente](container-registry-auto-purge.md) as imagens e os manifestos selecionados de um registro

* Saiba mais sobre as opções para [Bloquear imagens e manifestos](container-registry-image-lock.md) em um registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
