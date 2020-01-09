---
title: Red Hat Enterprise Linux imagens traga seu próprio assinatura | Microsoft Docs
description: Saiba mais sobre as imagens traga sua própria assinatura para Red Hat Enterprise Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: alsin
ms.openlocfilehash: afd59c44bea238b7dd6f116e491054f8b4a52d48
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486511"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-images-in-azure"></a>Red Hat Enterprise Linux imagens traga sua própria assinatura no Azure
As imagens do Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure por meio de um modelo pago pelo uso (PAYG) ou BYOS (traga sua própria assinatura). Este documento fornece uma visão geral das imagens BYOS no Azure.

## <a name="important-points-to-consider"></a>Pontos importantes a considerar

- As imagens RHEL BYOS fornecidas neste programa são imagens RHEL prontas para produção semelhantes às imagens RHEL PAYG na Galeria/Marketplace do Azure. O processo de registro para obter as imagens está em versão prévia.

- As imagens seguem nossas políticas atuais descritas em [Red Hat Enterprise Linux imagens no Azure](./redhat-images.md)

- As políticas de suporte padrão se aplicam a VMs criadas com base nessas imagens

- As VMs provisionadas a partir de imagens RHEL BYOS não carregam as taxas de RHEL associadas às imagens RHEL PAYG

- As imagens não são qualificadas, portanto, você deve usar o Subscription-Manager para registrar e assinar as VMs para obter atualizações do Red Hat diretamente

- No momento, não é possível alternar dinamicamente entre os modelos de cobrança BYOS e PAYG para imagens do Linux. Reimplantar a VM da respectiva imagem é necessária para alternar o modelo de cobrança

- Azure Disk Encryption (ADE) tem suporte nessas imagens RHEL BYOS. O suporte a ADE está atualmente em versão prévia. Você deve se registrar com o Red Hat usando o Subscription-Manager antes de configurar o ADE. Depois de registrado, para configurar o ADE, consulte: [habilitar Azure Disk Encryption para VMs IaaS do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-overview)

- Embora as imagens não sejam alteradas (além das atualizações e patches padrão), o processo de registro está em versão prévia e o fluxo será aprimorado ainda mais para simplificar o processo

- Você tem controle total das VMs já provisionadas a partir dessas imagens ou de seus instantâneos, independentemente da implementação final

## <a name="requirements-and-conditions-to-access-the-rhel-byos-images"></a>Requisitos e condições para acessar as imagens RHEL BYOS

1. Familiarize-se com os termos do programa de acesso à nuvem [Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e registre-se na [página de registro de acesso à nuvem do Red Hat](https://access.redhat.com/cloude/manager/image_imports/new).

1. Conclua o [formulário de solicitação de acesso BYOS RHEL](https://aka.ms/rhel-byos). Você precisará ter disponível o número da sua conta do Red Hat, bem como suas assinaturas do Azure com as quais você deseja acessar as imagens RHEL BYOS com.

1. Após a revisão e a aprovação da Red Hat e da Microsoft, suas assinaturas do Azure serão habilitadas para acesso à imagem.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Ao concluir o formulário BYOS RHEL e aceitar os termos, a Red Hat validará sua elegibilidade para as imagens de BYOS dentro de três dias úteis e, se for válida, você receberá acesso às imagens BYOS dentro de um dia útil posteriormente.

## <a name="use-the-rhel-byos-images-from-the-azure-portal"></a>Usar as imagens BYOS do RHEL no portal do Azure

1. Depois que sua assinatura estiver habilitada para imagens RHEL BYOS, você poderá localizá-la no [portal do Azure](https://portal.azure.com) navegando para **criar um recurso** e, em seguida, **ver tudo**.

1. Na parte superior da página, você verá que tem ofertas privadas.

    ![Ofertas privadas do Marketplace](./media/rhel-byos-privateoffers.png)

1. Você pode clicar no link roxo ou rolar para baixo até a parte inferior da página para ver suas ofertas particulares.

1. O restante do provisionamento na interface do usuário não será diferente de qualquer outra imagem do Red Hat existente. Escolha a versão do RHEL e siga os prompts para provisionar sua VM. Esse processo também permitirá que você aceite os termos da imagem na etapa final.

>[!NOTE]
>Essas etapas até o momento não habilitarão sua imagem BYOS do RHEL para implantação programática – uma etapa adicional será necessária, conforme descrito na seção "informações adicionais" abaixo.

O restante deste documento se concentra no método CLI para provisionar e aceitar os termos da imagem. A interface do usuário e a CLI são totalmente intercambiáveis no que diz respeito ao resultado final (uma VM BYOS de RHEL provisionada) está preocupada.

## <a name="use-the-rhel-byos-images-from-the-azure-cli"></a>Usar as imagens do RHEL BYOS do CLI do Azure
O seguinte conjunto de instruções orientará você pelo processo de implantação inicial para uma VM RHEL usando o CLI do Azure. Estas instruções pressupõem que você tenha o [CLI do Azure instalado](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de usar todas as letras minúsculas nas referências de Publicador, oferta, plano e imagem para todos os comandos a seguir

1. Verifique se você está na sua assinatura desejada:
    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para sua VM BYOS do RHEL:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos da imagem:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Esses termos precisam ser aceitos *uma vez por assinatura do Azure, por SKU de imagem*.

1. Adicional Valide sua implantação de VM com o seguinte comando:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Provisione sua VM executando o mesmo comando acima sem o argumento `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Use SSH em sua VM e verifique se você tem uma imagem não qualificada. PARA fazer isso, execute `sudo yum repolist`. A saída solicitará que você use Subscription-Manager para registrar a VM com o Red Hat.

## <a name="additional-information"></a>Informação adicional
- Se você tentar provisionar uma VM em uma assinatura que não está habilitada para esta oferta, você receberá o erro a seguir e deverá contatar a Microsoft ou a Red Hat para habilitar sua assinatura.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Se você criar um instantâneo da imagem RHEL BYOS e publicar a imagem na [Galeria de imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), será necessário fornecer informações de plano que correspondam à origem original do instantâneo. Por exemplo, o comando pode ser semelhante a (Observe os parâmetros de plano na linha final):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Se você estiver usando a automação para provisionar VMs das imagens RHEL BYOS, será necessário fornecer parâmetros de plano semelhantes ao que foi mostrado acima. Por exemplo, se você estiver usando Terraform, você forneceria as informações do plano em um [bloco de plano](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [infraestrutura de atualização do Red Hat do Azure](./redhat-rhui.md).
* Para saber mais sobre as imagens do Red Hat no Azure, vá para a [página de documentação](./redhat-images.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .