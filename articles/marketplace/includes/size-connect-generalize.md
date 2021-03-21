---
title: incluir ficheiro
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c60d2a9b13cce9251ff0f730081a9d677206770d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630125"
---
## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para isso, o sistema operativo VHD deve ser generalizado, uma operação que remove todos os identificadores e controladores de software específicos de instância de um VM.

### <a name="for-windows"></a>Para Windows

Os discos WINDOWS OS são generalizados com a ferramenta [sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Se atualizar ou reconfigurar o SO, terá de voltar a executar sysprep.

> [!WARNING]
> Depois de executar o sysprep, desligue o VM até ser implantado porque as atualizações podem ser executadas automaticamente. Esta paralisação evitará que atualizações subsequentes esveçam alterações específicas do sistema operativo ou dos serviços instalados. Para obter mais informações sobre a execução do sysprep, consulte [passos para generalizar um VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado. Para mais detalhes, consulte [Como criar uma imagem de uma máquina virtual ou VHD](../../virtual-machines/linux/capture-image.md). Pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

1. Retire o agente Azure Linux.
    1. Ligue-se ao seu VM Linux utilizando um cliente SSH.
    2. Na janela SSH, insira este comando: `sudo waagent –deprovision+user` .
    3. Tipo Y para continuar (pode adicionar o parâmetro de força ao comando anterior para evitar o passo de confirmação).
    4. Após o comando completo, **introduza saída** para fechar o cliente SSH.
2. Pare a máquina virtual.
    1. No portal Azure, selecione o seu grupo de recursos (RG) e desatribua o VM.
    2. O seu VM está agora generalizado e pode criar um novo VM utilizando este disco VM.

### <a name="capture-image"></a>Imagem de captura

Assim que o seu VM estiver pronto, pode capturá-lo numa galeria de imagens partilhada em Azure. Siga os passos abaixo para capturar:

1. No [portal Azure,](https://ms.portal.azure.com/)aceda à página da sua Máquina Virtual.
2. Selecione **Capture**.
3. Em **Imagem partilhada para galeria de imagens Partilhadas,** selecione **Sim, partilhe-a numa galeria como uma versão de imagem**.
4. Em **Estado do sistema operativo** selecione Generalizado.
5. Selecione uma galeria de imagens Target ou **Crie Novo**.
6. Selecione uma definição de imagem alvo ou **crie novo**.
7. Forneça um **número de versão** para a imagem.
8. Selecione **Review + crie** para rever as suas escolhas.
9. Uma vez aprovada a validação, **selecione Criar**.

Para publicar, a conta de editor deve ter um Proprietário acesso ao SIG. Para conceder acesso:

1. Vá à Galeria de Imagens Partilhadas.
2. Selecione **o controlo de acesso** (IAM) no painel esquerdo.
3. **Selecione Adicionar** e, em seguida, **Adicionar a atribuição de função**.
4. Selecione uma **função** ou **proprietário**.
5. No âmbito **do Access de Atribuir acesso a** **utilizador, grupo ou principal de serviço** selecionados.
6. Selecione o e-mail Azure da pessoa que vai publicar a imagem.
7. Selecione **Guardar**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Exibe a janela de atribuição de funções de adicionar.":::

> [!NOTE]
> Não precisa gerar URIs SAS, pois agora pode publicar uma Imagem SIG no Partner Center. No entanto, se ainda precisar de se referir aos passos de geração SAS URI, consulte [Como gerar um SAS URI para uma imagem VM](../azure-vm-get-sas-uri.md).
