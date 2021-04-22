---
title: Início Rápido do Microsoft Azure Data Box Disk| Microsoft Docs
description: Utilize este início rápido para implementar rapidamente o seu Azure Data Box Disk no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 241b7c0c07d1fbaa6a43c6be4b264424612f538a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869046"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Início Rápido: Implementar o Azure Data Box com o portal do Azure

Este início rápido descreve como implementar o Azure Data Box Disk através do portal do Azure. Os passos incluem como criar uma encomenda, receber discos, desembalar, ligar e copiar dados para discos para serem carregados para o Azure.

Para uma implementação passo a passo detalhada e instruções de controlo, aceda a [Tutorial: Encomende o Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

Este guia orienta-o pelos passos da utilização do Azure Data Box Disk no portal do Azure. Este guia ajuda a responder às seguintes perguntas.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que a sua subscrição está ativada para o serviço Azure Data Box. Para ativar a sua subscrição para este serviço, [Inscreva-se no serviço](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Rever pré-requisitos**: Verifique o número de discos e cabos, o sistema operativo e outro software.
> - **Ligar e desbloquear**: Ligue o dispositivo e desbloqueie o disco para copiar os dados.
> - **Copiar dados para o disco e validar**: Copie os dados para os discos nas pastas pré-criadas.
> - **Devolver os discos**: Devolva os discos para o centro de dados do Azure onde os dados são carregados para a sua conta de armazenamento.
> - **Verificar os dados no Azure**: Verifique se os dados foram carregados para a sua conta de armazenamento antes de os eliminar do servidor de dados de origem.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Encomenda

### <a name="portal"></a>[Portal](#tab/azure-portal)

Este passo demora cerca de 5 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure. 
2. Selecione uma subscrição ativada para este serviço e escolha o tipo de transferência, como **Importar**. Indique o **País de origem** onde os dados residem e a **Região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Disk**. A capacidade máxima da solução é 35 TB e pode criar várias encomendas de discos para tamanhos de dados superiores.  
4. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, os discos são preparados para envio.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize estes comandos do CLI do Azure para criar um trabalho do Data Box Disk.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou utilize um grupo de recursos existente:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Utilize o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento ou utilize uma conta de armazenamento existente:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Execute o comando [az databox job create](/cli/azure/databox/job#az_databox_job_create) para criar um trabalho do Data Box com o SKU DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Execute o comando [az databox job update](/cli/azure/databox/job#az_databox_job_update) para atualizar um trabalho, como neste exemplo, em que altera o nome e o e-mail de contacto:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Execute o comando [az databox job show](/cli/azure/databox/job#az_databox_job_show) para obter informações sobre o trabalho:

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Utilize o comando [az databox job list]( /cli/azure/databox/job#az_databox_job_list) para ver todos os trabalhos do Data Box para um grupo de recursos:

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Execute o comando [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) para cancelar um trabalho:

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Execute o comando [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) para eliminar um trabalho:

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Utilize o comando [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) para listar as credenciais para um trabalho do Data Box:

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Assim que a encomenda for criada, o dispositivo é preparado para envio.

---

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
    3. Execute a ferramenta de desbloqueio do Data Box Disk e indique a chave de acesso. Para qualquer reinserção de discos, execute a ferramenta de desbloqueio novamente e forneça a chave de acesso. **Não utilize a caixa de diálogo do BitLocker ou a chave do BitLocker para desbloquear o disco.** Para obter mais informações sobre como desbloquear discos, aceda a [Desbloquear discos num cliente Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos num cliente Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. A letra de unidade atribuída ao disco é apresentada pela ferramenta. Anote a letra de unidade do disco. Esta será utilizada nos passos subsequentes.

## <a name="copy-data-and-validate"></a>Copiar os dados e validar

O tempo de conclusão desta operação depende do tamanho dos dados.

1. A unidade contém as pastas *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* e *DataBoxDiskImport*. Arraste e cole para copiar os dados que têm de ser importados como blobs de blocos para a pasta *BlockBlob*. Da mesma forma, arraste e cole os dados como VHD/VHDX para a pasta *PageBlob* e os dados adequados para *AzureFile*. Copie os VHDs que pretende carregar como discos geridos para uma pasta em *ManagedDisk*.

    É criado um contentor na conta de armazenamento do Azure para cada subpasta nas pastas *BlockBlob* e *PageBlob*. É criada uma partilha de ficheiros para uma subpasta em *AzureFile*.

    Todos os ficheiros nas pastas *BlockBlob* e *PageBlob* são copiados para um contentor predefinido `$root` na conta de armazenamento do Azure. Copie os ficheiros para uma pasta dentro do *AzureFile*. Todos os ficheiros copiados diretamente para a pasta *AzureFile* falham e são carregados como blobs de blocos.

    > [!NOTE]
    > - Todos os contentores, blobs e ficheiros devem cumprir as [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se estas regras não forem seguidas, o carregamento de dados para o Azure falhará.
    > - Garanta que os ficheiros não excedem ~4,75 TiB para blobs de blocos, ~8 TiB para blobs de páginas e ~1 TiB para Ficheiros do Azure.

2. **(Opcional, mas recomendado)** Após a conclusão da cópia, é altamente recomendável que, no mínimo, execute `DataBoxDiskValidation.cmd` na pasta *DataBoxDiskImport* e selecione a opção 1 para validar os ficheiros. Também recomendamos que, se o tempo permitir, utilize a opção 2 para gerar também somas de verificação para validação (pode levar tempo consoante o tamanho dos dados). Estes passos minimizam as hipóteses de falhas ao carregar os dados para o Azure.
3. Remova a unidade em segurança.

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

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou o Azure Data Box Disk para ajudar a importar os seus dados para o Azure. Para saber mais sobre a gestão do Azure Data Box Disk, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Data Box Disk](data-box-portal-ui-admin.md)

::: zone-end
