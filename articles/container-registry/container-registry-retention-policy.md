---
title: Política para manter manifestos não testados
description: Saiba como ativar uma política de retenção no seu registo de contentores Azure, para a eliminação automática de manifestos não com marcação após um período definido.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83683452"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Definir uma política de retenção para manifestos não marcados

O Registo do Contentor Azure dá-lhe a opção de definir uma política de *retenção* para manifestos de imagem armazenados que não tenham etiquetas associadas *(manifestos não marcados).* Quando uma política de retenção está ativada, os manifestos não marcados no registo são automaticamente eliminados após alguns dias definidos. Esta funcionalidade impede que o registo se encha de artefactos que não são necessários e ajuda a economizar nos custos de armazenamento. Se o `delete-enabled` atributo de um manifesto não marcado for definido, o manifesto não pode ser apagado, e a política de `false` retenção não se aplica.

Pode utilizar o Azure Cloud Shell ou uma instalação local do CLI Azure para executar os exemplos de comando neste artigo. Se quiser usá-lo localmente, é necessário utilizar a versão 2.0.74 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Uma política de retenção é uma característica dos registos de contentores **Premium.** Para obter informações sobre os níveis de serviço de registo, consulte [os níveis de serviço de registo de contentores Azure.](container-registry-skus.md)

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [algumas limitações aplicam-se](#preview-limitations). As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

> [!WARNING]
> Definir uma política de retenção com dados de imagem eliminados é IRRECONHECÍVEL. Se tiver sistemas que puxem imagens por manifesta digestão (em oposição ao nome da imagem), não deve definir uma política de retenção para manifestos não marcados. A eliminação de imagens não identificadas impedirá que esses sistemas retirem as imagens do seu registo. Em vez de puxar pelo manifesto, considere a adoção de um esquema *de marcação único,* uma [melhor prática recomendada.](container-registry-image-tag-version.md)

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Só é possível definir uma política de retenção para manifestos não marcados.
* Atualmente, a política de retenção aplica-se apenas aos manifestos que não são combatidos *após* a ativação da política. Os manifestos não registados existentes no registo não estão sujeitos à apólice. Para eliminar os manifestos não registados existentes, consulte exemplos em [Apagar imagens de contentores no Registo de Contentores Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Sobre a política de retenção

O Registo do Contentor Azure faz a contagem de referência para manifestos no registo. Quando um manifesto não é pontuado, verifica a política de retenção. Se uma política de retenção estiver ativada, é colocada uma operação de exclusão manifesta, com uma data específica, de acordo com o número de dias definidos na apólice.

Um trabalho separado de gestão de filas processa constantemente mensagens, escalando conforme necessário. Como exemplo, suponha que não tenha registado dois manifestos, com 1 hora de diferença, num registo com uma política de retenção de 30 dias. Duas mensagens seriam na fila. Então, 30 dias depois, com aproximadamente 1 hora de intervalo, as mensagens seriam recuperadas da fila e processadas, assumindo que a apólice ainda estava em vigor.

## <a name="set-a-retention-policy---cli"></a>Definir uma política de retenção - CLI

O exemplo que se segue mostra como utilizar o CLI Azure para definir uma política de retenção para manifestos não marcados num registo.

### <a name="enable-a-retention-policy"></a>Permitir uma política de retenção

Por predefinição, nenhuma política de retenção é definida num registo de contentores. Para definir ou atualizar uma política de retenção, executar o comando [de atualização de retenção az acr config][az-acr-config-retention-update] no CLI Azure. Pode especificar um número de dias entre 0 e 365 para reter os manifestos não testados. Se não especificar um número de dias, o comando define um padrão de 7 dias. Após o período de retenção, todos os manifestos não registados no registo são automaticamente eliminados.

O exemplo a seguir define uma política de retenção de 30 dias para manifestos não registados no *miogrísta do*registo:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

O exemplo a seguir define uma política para apagar qualquer manifesto no registo assim que não for anotado. Crie esta política definindo um período de retenção de 0 dias. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validar uma política de retenção

Se ativar a política anterior com um período de retenção de 0 dias, pode verificar rapidamente se os manifestos não com marcação são eliminados:

1. Empurre uma imagem de teste `hello-world:latest` para o seu registo ou substitua outra imagem de teste à sua escolha.
1. Desata a `hello-world:latest` imagem, por exemplo, usando o comando [de desmarcação do repositório az acr.][az-acr-repository-untag] O manifesto não registado permanece no registo.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Em poucos segundos, o manifesto não testado é apagado. Pode verificar a eliminação listando manifestos no repositório, por exemplo, utilizando o comando [az acr repositório de manifestos.][az-acr-repository-show-manifests] Se a imagem de teste foi a única no repositório, o próprio repositório é apagado.

### <a name="disable-a-retention-policy"></a>Desativar uma política de retenção

Para ver a política de retenção definida num registo, executar o comando [de retenção az acr config show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Para desativar uma política de retenção num registo, executar o comando [de atualização de retenção az acr config][az-acr-config-retention-update] e `--status disabled` definir:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Definir uma política de retenção - portal

Também pode definir a política de retenção de um registo no [portal Azure](https://portal.azure.com). O exemplo que se segue mostra como usar o portal para definir uma política de retenção para manifestos não marcados num registo.

### <a name="enable-a-retention-policy"></a>Permitir uma política de retenção

1. Navegue para o seu registo de contentores Azure. Em **Políticas**, selecione **Retenção** (Pré-visualização).
1. Em **Status**, selecione **Enabled**.
1. Selecione um número de dias entre 0 e 365 para reter os manifestos não testados. Selecione **Guardar**.

![Permitir uma política de retenção no portal Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Desativar uma política de retenção

1. Navegue para o seu registo de contentores Azure. Em **Políticas**, selecione **Retenção** (Pré-visualização).
1. Em **Status**, selecione **Disabled**. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre opções para [apagar imagens e repositórios](container-registry-delete.md) no Registo de Contentores do Azure

* Saiba como [expurgar automaticamente](container-registry-auto-purge.md) imagens selecionadas e manifesta-se a partir de um registo

* Saiba mais sobre opções para [bloquear imagens e manifestos](container-registry-image-lock.md) num registo

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
