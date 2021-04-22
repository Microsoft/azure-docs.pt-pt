---
title: Início Rápido do Microsoft Azure Data Box Heavy| Microsoft Docs
description: Neste início rápido, saiba como implementar o Azure Data Box Heavy no portal do Azure, incluindo como instalar os cabos, configurar e copiar dados para carregar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 8c418f7cbeb56b94b7a85b12e833301b979bff32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871561"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Início Rápido: Implementar o Azure Data Box Heavy com o portal do Azure

Este início rápido descreve como implementar o Azure Data Box Heavy com o portal do Azure. Os passos incluem como ligar os cabos, configurar e copiar os dados para o Data Box Heavy, para que este os carregue para o Azure. O início rápido é efetuado no portal do Azure e na IU da Web local do dispositivo.

Para uma implementação passo a passo detalhada e instruções de controlo, aceda ao [Tutorial: Encomendar o Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Conclua os pré-requisitos de configuração seguintes para o local de instalação, o dispositivo e o serviço Data Box antes de implementar o dispositivo.

### <a name="for-installation-site"></a>Para o local de instalação

Antes de começar, certifique-se de que:

- O dispositivo passa em todas as portas de entrada. As dimensões do dispositivo são: largura: 66 cm; comprimento: 122 cm; altura: 71 cm.
- Possui um elevador ou de uma rampa de acesso se planear instalar o dispositivo num piso que não seja o rés-do-chão.
- Dispõe de duas pessoas para mover o dispositivo. O dispositivo pesa aproximadamente 226 kg e inclui rodas.
- Tem um local plano no datacenter próximo de uma ligação de rede disponível que possa acomodar um dispositivo com estas dimensões.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:

- Reviu as [diretrizes de segurança do Data Box Heavy](data-box-safety.md).
- Tem um computador anfitrião ligado à rede do datacenter. O Data Box Heavy irá copiar os dados desse computador. O computador anfitrião deve ser executado num [sistema operativo suportado](data-box-heavy-system-requirements.md).
- Tem um portátil com cabo RJ-45 para se ligar à IU local e configurar o dispositivo. Utilize o portátil para configurar cada nó do dispositivo uma vez.
- O datacenter tem rede de alta velocidade e tem, pelo menos, uma ligação de 10 GbE.
- Precisa de um cabo de 40 Gbps ou de 10 Gbps por nó de dispositivo. Escolha cabos que sejam compatíveis com a interface de rede Mellanox MCX314A-BCCT:
    - Para o cabo de 40 Gbps, a extremidade do cabo que liga ao dispositivo tem de ser QSFP+.
    - Para o cabo de 10 Gbps, precisa de um cabo SFP+ que se ligue a um comutador de 10 G numa extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que liga ao dispositivo.
- Os cabos de alimentação estão incluídos numa bandeja na parte traseira do dispositivo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Encomenda

### <a name="portal"></a>[Portal](#tab/azure-portal)

Este passo demora cerca de 5 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure.
2. Selecione uma subscrição existente ativada para este serviço e escolha o tipo de transferência, como **Importar**. Indique o **País de origem** onde os dados residem e a **Região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Heavy**. A capacidade máxima utilizável é de 770 TB e pode criar várias encomendas para tamanhos de dados superiores.
4. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, o dispositivo é preparado para envio.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize estes comandos do CLI do Azure para criar um trabalho do Data Box Heavy.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Execute o comando [az group create](/cli/azure/group#az_group_create) para criar um grupo de recursos ou utilize um grupo de recursos existente:

   ```azurecli
   az group create --name databox-rg --location westus 
   ```

1. Utilize o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) para criar uma conta de armazenamento ou utilize uma conta de armazenamento existente:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Execute o comando [az databox job create](/cli/azure/databox/job#az_databox_job_create) para criar um trabalho do Data Box com o valor de **--sku** de `DataBoxHeavy`:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxheavy-job \
       --location westus --sku DataBoxHeavy --contact-name "Jim Gan" --phone 4085555555 \
       --city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA --storage-account databoxtestsa \
       --staging-storage-account databoxtestsa --resource-group-for-managed-disk rg-for-md
   ```

   > [!NOTE]
   > Certifique-se de que a sua subscrição suporta o Data Box Heavy.

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

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>Instalar os cabos e ligar ao dispositivo

Depois de rever os pré-requisitos, vai instalar os cabos e ligar-se ao dispositivo.

::: zone-end

## <a name="cable-for-power"></a>Cabo de alimentação

Este passo demora cerca de 5 minutos.

Quando receber o Data Box Heavy, siga os passos abaixo para instalar os cabos para ligar e ativar o dispositivo.

1. Se existirem quaisquer evidências de que o dispositivo foi adulterado ou está danificado, não prossiga. Contacte o Suporte da Microsoft para enviar-lhe um dispositivo de substituição.
2. Mova o dispositivo para o local de instalação e bloqueie as rodas traseiras.
3. Ligue todos os quatro cabos de alimentação às fontes de energia na parte traseira do dispositivo.
4. Utilize os botões para ligar/desligar no plano frontal para ativar os nós do dispositivo.

## <a name="cable-first-node-for-network"></a>Ligar o cabo do primeiro nó à rede

Este passo demora entre 10 a 15 minutos.

1. Utilize o cabo de rede RJ-45 CAT 6 para ligar o seu computador anfitrião à porta de gestão (MGMT) no dispositivo.
2. Utilize o cabo de cobre QSFP+ Twinax para ligar uma interface de rede de, pelo menos, 40 Gbps (em vez da de 1 Gbps), DATA1 ou DATA2 para os dados. Se estiver a utilizar um comutador de 10 Gbps, utilize um cabo de cobre Twinax SFP+ com um adaptador QSFP+ para SFP+ (o adaptador QSA) para ligar a interface de rede de 40 Gbps para os dados.
3. Instale os cabos do dispositivo conforme mostrado abaixo.  

    ![Cabos do Data Box Heavy instalados](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Configurar o primeiro nó

Este passo demora entre 5 a 7 minutos.

1. Para obter a palavra-passe do dispositivo, aceda a **Geral > Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com). A mesma palavra-passe é utilizada para ambos os nós do dispositivo.
2. Atribua um endereço IP estático 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que está a utilizar para ligar ao Data Box Heavy. Aceda à IU da Web local do dispositivo em `https://192.168.100.10`. A ligação pode demorar até 5 minutos depois de ligar o dispositivo.
3. Inicie sessão com a palavra-passe do portal do Azure. Verá um erro que indica um problema com o certificado de segurança do site. Siga as instruções específicas do browser para avançar para a página Web.
4. Por predefinição, as definições de rede das interfaces (excluindo a MGMT) estão configuradas como DHCP. Se necessário, pode configurar estas interfaces como estática e fornecer um endereço IP.

## <a name="cable-and-configure-the-second-node"></a>Instalar os cabos e configurar o segundo nó

Este passo demora entre 15 a 20 minutos.

Siga os passos utilizados para o primeiro nó para instalar os cabos e configurar o segundo nó no dispositivo.  


::: zone target = "docs"

## <a name="copy-data"></a>Copiar dados

O tempo de conclusão desta operação depende do tamanho dos dados e da velocidade da rede sobre a qual os dados são copiados.
 
1. Copie os dados para ambos os nós do dispositivo através das interfaces de dados de 40 Gbps em paralelo.

    - Se estiver a utilizar um sistema anfitrião do Windows, utilize uma ferramenta de cópia de ficheiros compatível com SMB, como o [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
    - Para o anfitrião NFS, utilize o comando `cp` ou `rsync` para copiar os dados.
2. Ligue-se às partilhas no dispositivo através do caminho:`\\<IP address of your device>\ShareName`. Para obter as credenciais de acesso às partilhas, aceda à página **Ligar e copiar** na IU da Web local do Data Box Heavy.
3. Confirme que os nomes das partilhas e das pastas e os dados cumprem as diretrizes descritas nos [Limites de serviços do Armazenamento do Microsoft Azure e do Data Box Heavy](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Preparar para enviar

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Uma vez concluída a cópia dos dados sem erros, aceda à página **Preparação para envio** na IU da Web local e comece a preparação para envio.
2. Uma vez concluída a **Preparação para envio** com êxito em ambos os nós, desligue o dispositivo da IU da Web local.

## <a name="ship-to-azure"></a>Enviar para o Azure

Esta operação demora entre 15 a 20 minutos.

1. Remova os cabos e coloque-os na bandeja na parte traseira do dispositivo.
2. Agende uma recolha com a transportadora regional.
3. Contacte as [Operações do Data Box](mailto:DataBoxOps@microsoft.com) para fornecer as informações sobre a recolha e para obter a guia de remessa de devolução.
4. A guia de remessa de devolução deve estar visível no painel transparente frontal do dispositivo.

## <a name="verify-data"></a>Verificar os dados

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Quando o dispositivo do Data Box Heavy está ligado à rede do datacenter do Azure, os dados são carregados automaticamente para o Azure.
2. O serviço Data Box notifica-o de que a cópia de dados está concluída através do portal do Azure.

    1. Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
    2. Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

## <a name="clean-up-resources"></a>Limpar recursos

Este passo demora entre 2 a 3 minutos.

- Pode cancelar a encomenda do Data Box Heavy no portal do Azure antes de ser processada. Assim que a encomenda estiver processada, não é possível cancelá-la. A encomenda avança até atingir a fase de conclusão. Para cancelar a encomenda, aceda a **Descrição geral** e clique em **Cancelar** na barra de comandos.

- Pode eliminar a encomenda assim que o estado for apresentado como **Concluído** ou **Cancelado** no portal do Azure. Para eliminar a encomenda, aceda a **Descrição geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou um Data Box Heavy para ajudar a importar os dados para o Azure. Para saber mais sobre a gestão do Azure Data Box Heavy, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Data Box Heavy](data-box-portal-admin.md)

::: zone-end
