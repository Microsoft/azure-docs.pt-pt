---
title: Crie uma oferta de máquina virtual Azure no Azure Marketplace usando a sua própria imagem
description: Aprenda a publicar uma oferta de máquina virtual ao Azure Marketplace usando a sua própria imagem.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200464"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Como criar uma máquina virtual usando a sua própria imagem

Este artigo descreve como criar e implementar uma imagem de máquina virtual (VM) fornecida pelo utilizador.

> [!NOTE]
> Antes de iniciar este procedimento, reveja os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) para as ofertas de VM Azure, incluindo os requisitos de disco rígido virtual (VHD).

Para utilizar uma imagem base aprovada, siga as instruções na [Criar uma imagem VM a partir de uma base aprovada](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurar a VM

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="size-the-vhds"></a>Tamanho dos VHDs

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Realizar mais verificações de segurança

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para isso, o sistema operativo VHD deve ser generalizado, uma operação que remove todos os identificadores e controladores de software específicos de instância de um VM.

## <a name="bring-your-image-into-azure"></a>Traga a sua imagem para Azure

Há três maneiras de trazer a sua imagem para Azure:

1. Faça o upload do vhd para uma Galeria de Imagens Partilhadas (SIG).
1. Faça o upload do vhd para uma conta de armazenamento Azure.
1. Extrair o vhd de uma Imagem Gerida (se utilizar serviços de construção de imagens).

As três secções seguintes descrevem estas opções.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Opção 1: Carregar o VHD como Galeria de Imagens Partilhadas

1. Faça o upload vhd(s) para a conta de armazenamento.
2. No portal Azure, procure **implementar um modelo personalizado.**
3. Selecione **Construa o seu próprio modelo no editor.**
4. Copie o seguinte modelo de Gestor de Recursos Azure (ARM).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Cole o modelo no editor.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Amostra de ecrã de código para VM.":::

1. Selecione **Guardar**.
1. Utilize os parâmetros desta tabela para completar os campos no ecrã que se segue.

| Parâmetros | Description |
| --- | --- |
| fonteStorageAccountResourceId | Identificação de recursos da conta de armazenamento de origem na qual reside o blob vhd.<br><br>Para obter o ID de recurso, aceda à sua **Conta de Armazenamento** no portal **Azure,** vá ao **Properties** e copie o valor **ResourceID.** |
| fonteBlobUri | Blob Uri do disco de sol vhd blob (deve estar na conta de armazenamento fornecida).<br><br>Para obter o URL blob, vá à sua **Conta de Armazenamento** no portal **Azure,** vá à sua **bolha** e copie o valor **URL.** |
| fonteBlobDataDisk0Uri | Blob Uri do disco de dados vhd blob (deve estar na conta de armazenamento fornecida). Se não tiver um disco de dados, remova este parâmetro do modelo.<br><br>Para obter o URL blob, vá à sua **Conta de Armazenamento** no portal **Azure,** vá à sua **bolha** e copie o valor **URL.** |
| fonteBlobDataDisk1Uri | Blob Uri de dissiudo adicional do disco de dados vhd blob (deve estar na conta de armazenamento fornecida). Se não tiver um disco de dados adicional, remova este parâmetro do modelo.<br><br>Para obter o URL blob, vá à sua **Conta de Armazenamento** no portal **Azure,** vá à sua **bolha** e copie o valor **URL.** |
| galeriaName | Nome da Galeria de Imagens Partilhadas |
| galeriaImageDefinitionName | Nome da Definição de Imagem |
| galeriaImageversionName | Nome da Versão de Imagem a criar, neste formato: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Mostra a janela de implantação personalizada.":::

8. Selecione **Rever + criar**. Assim que a validação terminar, **selecione Criar**.

> [!TIP]
> A conta de editor deve ter acesso "Proprietário" para publicar a Imagem SIG. Se necessário, siga os passos abaixo para conceder acesso:
>
> 1. Aceda à Galeria de Imagens Partilhadas (SIG).
> 2. Selecione **o controlo de acesso** (IAM) no painel esquerdo.
> 3. **Selecione Adicionar** e, em **seguida, Adicione a atribuição de função**.
> 4. Para **função**, selecione **Proprietário**.
> 5. Para **atribuir acesso a**, selecione **Utilizador, grupo ou principal de serviço**.
> 6. Insira o e-mail Azure da pessoa que publicará a imagem.
> 7. Selecione **Guardar**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="É mostrada a janela de atribuição de função de adicionar.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Opção 2: Fazer o upload do VHD para uma conta de armazenamento

Configure e prepare o VM para ser carregado como descrito no [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ou [Criar e Carregar um Linux VHD](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Opção 3: Extrair o VHD da Imagem Gerida (se utilizar serviços de construção de imagens)

Se estiver a utilizar um serviço de construção de imagens como o [Packer,](https://www.packer.io/)poderá ter de extrair o VHD da imagem. Não há forma direta de fazer isto. Terá de criar um VM e extrair o VHD do disco VM.

## <a name="create-the-vm-on-the-azure-portal"></a>Criar o VM no portal Azure

Siga estes passos para criar a imagem VM base no [portal Azure](https://ms.portal.azure.com/).

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. **Selecione + Adicione** para abrir o ecrã de máquina **virtual.**
4. Selecione a imagem da lista de dropdown ou **selecione Navegue em todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
5. Para criar um **Gen 2** VM, vá ao separador **Advanced** e selecione a opção **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho do VM para implementar.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione um tamanho VM recomendado para a imagem selecionada.":::

7. Forneça os outros detalhes necessários para criar o VM.
8. Selecione **Review + crie** para rever as suas escolhas. Quando aparecer a mensagem **de validação,** selecione **Criar**.

O Azure começa a aturar a máquina virtual especificada. Acompanhe o seu progresso selecionando o separador **Máquinas Virtuais** no menu esquerdo. Depois de criado, o estado das alterações da Máquina Virtual para **executar**.

## <a name="connect-to-your-vm"></a>Ligar à VM

Consulte a seguinte documentação para ligar ao seu [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passos seguintes

- [Teste a sua imagem VM](azure-vm-image-test.md) para garantir que cumpre os requisitos de publicação do Azure Marketplace. Isto é opcional.
- Se não quiser testar a sua imagem em VM, inscreva-se no [Partner Center](https://partner.microsoft.com/) e publique a Imagem SIG (opção #1).
- Se seguiu a opção #2 ou #3, [Gere o SAS URI](azure-vm-get-sas-uri.md).
- Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [a VM FAQ para o Azure Marketplace](azure-vm-create-faq.md).
