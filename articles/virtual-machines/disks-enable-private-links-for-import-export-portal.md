---
title: Portal Azure - Restringir o acesso à importação/exportação a discos geridos com Links Privados (pré-visualização)
description: Ativar Links Privados (pré-visualização) para os seus discos geridos com o portal Azure. Permitindo-lhe exportar e importar discos de forma segura dentro apenas da sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 75b5ba995ff87649ec8a7a96a7c816bf2bec7e44
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535816"
---
# <a name="azure-portal---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>Portal Azure - Restringir o acesso à importação/exportação de discos geridos com Links Privados (pré-visualização)

Pode gerar um URI de assinatura de acesso compartilhado (SAS) para discos geridos não ligados e instantâneos para exportar os dados para outra região para expansão regional, recuperação de desastres e ler os dados para análise forense. Também pode utilizar o SAS URI para carregar diretamente o VHD para um disco vazio a partir do seu local.  Agora pode aproveitar [links privados](../private-link/private-link-overview.md) (pré-visualização) para restringir a exportação e importação para Discos Geridos apenas a partir da sua rede virtual Azure. Além disso, tem a certeza de que os dados nunca passam pela internet pública e viaja sempre dentro da rede segura da espinha dorsal da Microsoft quando utiliza Links Privados. 

Pode criar um recurso de acesso ao disco e ligá-lo à sua rede virtual na mesma subscrição, criando um ponto final privado. Deve associar um disco ou um instantâneo a um disco de acesso para exportação e importação dos dados através de Links Privados. Além disso, deve definir a propriedade NetworkAccessPolicy do disco ou a snapshot para `AllowPrivate` . 

Pode definir a propriedade NetworkAccessPolicy `DenyAll` para evitar que alguém gera o SAS URI para um disco ou instantâneo. O valor padrão para a propriedade NetworkAccessPolicy é `AllowAll` .

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [virtual-machines-disks-private-links-regions](../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar pontos finais privados para exportar e importar discos geridos, tem de obter a funcionalidade ativada na sua subscrição. Envie um e-mail para mdprivatelinks@microsoft .com com os seus Ids de subscrição para obter a funcionalidade ativada para as suas subscrições.

Terá de observar a rede virtual do VM a que os seus discos estão ligados. A rede virtual é necessária ao configurar o ponto final privado.

## <a name="create-a-disk-access-resource"></a>Criar um recurso de acesso ao disco

1. Inscreva-se no portal Azure e navegue para **o Acesso** ao Disco com [este link.](https://aka.ms/disksprivatelinks)

    > [!IMPORTANT]
    > Tem de utilizar o [link fornecido](https://aka.ms/disksprivatelinks) para navegar na lâmina de acesso ao disco. Não é atualmente visível no portal público sem utilizar o link.

1. **Selecione + Adicione** para criar um novo recurso de acesso ao disco.
1. Na lâmina de criação, selecione a sua subscrição, um grupo de recursos, introduza um nome e selecione uma região.
1. Selecione **Rever + criar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Screenshot da lâmina de criação de acesso ao disco. Preencha o nome pretendido, selecione uma região, selecione um grupo de recursos e proceda":::

Quando o seu recurso tiver sido criado, navegue diretamente para ele.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Screenshot do botão de recurso Go no portal":::

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado

Agora que tem um recurso de acesso ao disco, pode usá-lo para lidar com o acesso às exportações/importações do seu disco, o que é feito através de pontos finais privados. Assim, terá de criar um ponto final privado e configurá-lo para acesso ao disco.

1. A partir do seu recurso de acesso ao disco, selecione **ligações de ponto final privados**.
1. Selecione **+ ponto final privado.**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Screenshot da lâmina de visão geral para o seu recurso de acesso ao disco. Destacam-se as ligações privadas de ponto final.":::

1. Selecionar um grupo de recursos
1. Preencha o nome e selecione a mesma região em que o seu recurso de acesso ao disco foi criado.
1. Selecione **Seguinte: >de recursos**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Screenshot do fluxo de trabalho de criação de ponto final privado, primeira lâmina. Se não selecionar a região apropriada, poderá encontrar problemas mais tarde.":::

1. Na lâmina **de recurso,** selecione **Connect to a Azure resource in my directy**.
1. Para **tipo de recurso** selecione **Microsoft.Compute/diskAccesses**
1. Para **obter recursos,** selecione o recurso de acesso ao disco que criou anteriormente
1. Deixe o **sub-recurso target** como **discos**
1. Selecione **seguinte : Configuração >**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Screenshot do fluxo de trabalho de criação de ponto final privado, segunda lâmina. Com todos os valores destacados (tipo de recurso, recurso, sub-recurso alvo)":::

1. Selecione a rede virtual para a quais pretende limitar a exportação de discos, outras redes virtuais não poderão exportar o seu disco.

    > [!NOTE]
    > Se tiver um grupo de segurança de rede (NGS) ativado para a sub-rede selecionada, será desativado apenas para pontos finais privados nesta sub-rede. Outros recursos nesta sub-rede continuarão a ter a aplicação da NSG.

1. Selecione a sub-rede apropriada
1. Selecione **Rever + criar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Screenshot do fluxo de trabalho de criação de ponto final privado, terceira lâmina. Rede virtual e sub-rede enfatizadas.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Ativar o ponto final privado no seu disco

1. Navegue para o disco que gostaria de configurar
1. **Selecione Networking**
1. Selecione **o ponto final privado (através do acesso ao disco)** e selecione o acesso ao disco que criou anteriormente.
1. Selecione **Guardar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Screenshot da lâmina de rede de disco gerida. Destacando a seleção de pontos finais privados, bem como o acesso ao disco selecionado. Guardar isto configura o seu disco para este acesso.":::

Já completou a configuração de Links Privados que pode utilizar ao importar/exportar o seu disco gerido.

## <a name="next-steps"></a>Próximos passos

- [FAQ para links privados](linux/faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportação/Cópia geriu instantâneos como VHD para uma conta de armazenamento em diferentes regiões com PowerShell](scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)