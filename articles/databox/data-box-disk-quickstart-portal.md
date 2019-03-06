---
title: Início Rápido do Microsoft Azure Data Box Disk| Microsoft Docs
description: Utilize este início rápido para implementar rapidamente o seu Azure Data Box Disk no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 02/26/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 298c5f718d10a318ff241cf000f24b5484485489
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408586"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Início rápido: Implementar o disco do Azure Data Box através do portal do Azure

Este início rápido descreve como implementar o Azure Data Box Disk através do portal do Azure. Os passos incluem como criar uma encomenda, receber discos, desembalar, ligar e copiar dados para discos para serem carregados para o Azure.

Para implementação passo a passo detalhada e instruções de controlo, aceda a [Tutorial: Solicite o disco do Azure Data Box](data-box-disk-deploy-ordered.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que a sua subscrição está ativada para o serviço Azure Data Box. Para ativar a sua subscrição para este serviço, [Inscreva-se no serviço](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [http://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Encomenda

Este passo demora cerca de 5 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure. 
2. Selecione uma subscrição ativada para este serviço e escolha o tipo de transferência, como **Importar**. Indique o **País de origem** onde os dados residem e a **Região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Disk**. A capacidade máxima da solução é 35 TB e pode criar várias encomendas de discos para tamanhos de dados superiores.  
4. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, os discos são preparados para envio.

## <a name="unpack"></a>Desembalar

Este passo demora cerca de 5 minutos.

O Data Box Disk é enviado por correio numa Caixa Expresso da UPS. Abra a caixa e verifique se tem:

- 1 a 5 discos USB embrulhados em plástico bolha.
- Um cabo de ligação por disco.
- Uma etiqueta de envio de devolução.

## <a name="connect-and-unlock"></a>Ligar e desbloquear

Este passo demora cerca de 5 minutos.

1. Utilize o cabo incluído para ligar o disco a um computador Windows/Linux com uma versão suportada. Para obter mais informações sobre as versões de SO suportadas, aceda a [Requisitos de sistema do Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Para desbloquear o disco:

    1. No portal do Azure, aceda a **Geral > Detalhes do Dispositivo** e obtenha a chave de acesso.
    2. Transfira e extraia a ferramenta de desbloqueio do Data Box Disk específica do sistema no computador utilizado para copiar os dados para discos. 
    3. Execute a ferramenta de desbloqueio do Data Box Disk e indique a chave de acesso. Para qualquer reinserção de discos, execute a ferramenta de desbloqueio novamente e forneça a chave de acesso. **Não utilize a caixa de diálogo do BitLocker ou a chave do BitLocker para desbloquear o disco.** Para obter mais informações sobre como desbloquear discos, aceda a [desbloquear discos no cliente Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [desbloquear discos no cliente de Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. A letra de unidade atribuída ao disco é apresentada pela ferramenta. Anote a letra de unidade do disco. Esta será utilizada nos passos subsequentes.

## <a name="copy-data-and-validate"></a>Copiar os dados e validar

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Contém a unidade *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk*, e *DataBoxDiskImport* pastas. Arraste e cole para copiar os dados que têm de ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, arrastar e soltar dados como VHD/VHDX *PageBlob* pasta e os dados apropriados para *AzureFile*. Copie os VHDs que pretende carregar como discos geridos para uma pasta sob *ManagedDisk*.

    É criado um contentor na conta de armazenamento do Azure para cada subpasta nas pastas *BlockBlob* e *PageBlob*. É criada uma partilha de ficheiros para uma sub-pasta sob *AzureFile*.

    Todos os ficheiros nas pastas *BlockBlob* e *PageBlob* são copiados para um contentor predefinido `$root` na conta de armazenamento do Azure. Copiar ficheiros para uma pasta dentro *AzureFile*. Todos os arquivos copiados diretamente para o *AzureFile* falharão de pasta e são carregados como blobs de blocos.

    > [!NOTE]
    > - Todos os contentores, blobs, arquivos e deverá obedecer [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se estas regras não forem seguidas, o carregamento de dados para o Azure falhará.
    > - Certifique-se de que arquivos excede ~4.75 TiB para blobs de blocos, TiB de cerca de 8 para blobs de páginas e ~ 1 TiB para ficheiros do Azure.

2. **(Opcional mas recomendado)**  Depois da cópia estiver concluída, recomendamos vivamente que, no mínimo, execute o `DataBoxDiskValidation.cmd` fornecida no *DataBoxDiskImport* pasta e selecione opção 1 para validar os ficheiros. Recomendamos também permitir esse tempo, utilize a opção 2 para também gerar somas de verificação de validação (poderá demorar algum tempo consoante o tamanho dos dados). Estes passos minimizar as hipóteses de qualquer falha ao carregar os dados para o Azure.
3. Remova com segurança a unidade.

## <a name="ship-to-azure"></a>Enviar para o Azure

Este passo demora entre 5 a 7 minutos.

1. Coloque todos os discos na embalagem original. Utilize a etiqueta de envio incluída. Se a etiqueta estiver danificada ou tiver sido perdida, transfira-a a partir do portal. Aceda a **Descrição geral** e clique em **Transferir etiqueta de envio** na barra de comandos.
2. Deixe a embalagem selada na localização de envio.  

O serviço Data Box Disk envia uma notificação por e-mail e atualiza o estado da encomenda no portal do Azure.

## <a name="verify-your-data"></a>Verificar os dados

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Quando o Data Box Disk está ligado à rede do datacenter do Azure, o carregamento de dados para o Azure é iniciado automaticamente.
2. O serviço Azure Data Box notifica-o de que a cópia de dados está concluída através do portal do Azure.
    
    1. Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
    2. Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

## <a name="clean-up-resources"></a>Limpar recursos

Este passo demora entre 2 a 3 minutos.

Para limpar, pode cancelar a encomenda do Data Box e, em seguida, eliminá-la.

- Pode cancelar a encomenda do Data Box no portal do Azure antes de ser processada. Assim que a encomenda estiver processada, não é possível cancelá-la. A encomenda avança até atingir a fase de conclusão.

    Para cancelar a encomenda, aceda a **Descrição geral** e clique em **Cancelar** na barra de comandos.  

- Pode eliminar a encomenda assim que o estado for apresentado como **Concluído** ou **Cancelado** no portal do Azure.

    Para eliminar a encomenda, aceda a **Descrição geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou o Azure Data Box Disk para ajudar a importar os seus dados para o Azure. Para saber mais sobre a gestão do Azure Data Box Disk, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Data Box Disk](data-box-portal-ui-admin.md)
