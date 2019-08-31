---
title: Início rápido para Microsoft Azure Data Box Heavy | Microsoft Docs
description: Saiba como implantar rapidamente seu Azure Data Box Heavy no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 433c3ac08d8d7d8158d4cc958979782e3b3192e1
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164424"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Início rápido: Implantar Azure Data Box Heavy usando o portal do Azure

Este guia de início rápido descreve como implantar o Azure Data Box Heavy usando o portal do Azure. As etapas incluem como conectar, configurar e copiar dados para Data Box Heavy para que ele seja carregado no Azure. O início rápido é efetuado no portal do Azure e na IU da Web local do dispositivo.

Para obter instruções detalhadas de implantação e acompanhamento passo a [passo, acesse tutorial: Ordem Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes pré-requisitos de configuração para o site de instalação, o serviço de Data Box e o dispositivo antes de implantar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, certifique-se de que:

- O dispositivo pode se ajustar a todos os seus entryways. As dimensões do dispositivo são: largura: 26 "comprimento: 48 "altura: 28 ".
- Você tem acesso ao dispositivo por meio de um elevador ou uma rampa se planeja instalar o em um andar diferente do chão.
- Você tem duas pessoas para lidar com o dispositivo. O dispositivo pesa aproximadamente cerca de 500 kg. e vem em rodas.
- Você tem um site simples no datacenter com proximidade com uma conexão de rede disponível que pode acomodar um dispositivo com essa superfície.

### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:

- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- A assinatura usada para o serviço de Data Box é o [Microsoft Enterprise Agreement (ea), o](https://azure.microsoft.com/pricing/enterprise-agreement/) [CSP (provedor de soluções de nuvem)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)ou o [Microsoft Azure sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Você tem acesso de proprietário ou colaborador à assinatura para criar uma ordem de Data Box Heavy.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:

- Você analisou as [diretrizes de segurança para seu data Box Heavy](data-box-safety.md).
- Você tem um computador host conectado à rede do datacenter. Data Box Heavy copiará os dados deste computador. O computador host deve executar um [sistema operacional com suporte](data-box-heavy-system-requirements.md).
- Você tem um laptop com cabo RJ-45 para se conectar à interface do usuário local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
- Seu datacenter tem rede de alta velocidade e você tem pelo menos conexão de 1 10 GbE.
- É necessário um cabo de 1 40 Gbps ou um cabo de 10 Gbps por nó de dispositivo. Escolha os cabos que são compatíveis com a interface de rede Mellanox MCX314A-BCCT:
    - Para o cabo de 40 Gbps, a extremidade do dispositivo do cabo precisa ser QSFP +.
    - Para o cabo de 10 Gbps, você precisa de um cabo SFP + que se conecte a um comutador de 10 G em uma extremidade, com um adaptador QSFP + para SFP + (ou o adaptador QSA) para o final que se conecta ao dispositivo.
- Os cabos de alimentação são incluídos em uma bandeja na parte posterior do dispositivo.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Encomenda

Este passo demora cerca de 5 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure.
2. Selecione uma subscrição existente ativada para este serviço e escolha o tipo de transferência, como **Importar**. Indique o **País de origem** onde os dados residem e a **Região de destino do Azure** para a transferência de dados.
3. Selecione **Data Box Heavy**. A capacidade utilizável máxima é de 770 TB e você pode criar vários pedidos para tamanhos de dados maiores.
4. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, o dispositivo é preparado para envio.

::: zone-end

::: zone target = "chromeless"

# <a name="cable-and-connect-to-your-device"></a>Cabo e conecte-se ao seu dispositivo

Depois de revisar os pré-requisitos, você ligará o cabo e se conectará ao seu dispositivo.

::: zone-end

## <a name="cable-for-power"></a>Cabo para energia

Esta etapa leva cerca de 5 minutos.

Ao receber a Data Box Heavy, execute as etapas a seguir para conectar o dispositivo para ligar e ligar o dispositivo.

1. Se existirem quaisquer evidências de que o dispositivo foi adulterado ou está danificado, não prossiga. Contacte o Suporte da Microsoft para enviar-lhe um dispositivo de substituição.
2. Mova o dispositivo para o site de instalação e bloqueie as rodas traseiras.
3. Conecte todos os quatro cabos de alimentação às fontes de alimentação na parte traseira do dispositivo.
4. Use os botões de energia no plano frontal para ativar os nós de dispositivo.

## <a name="cable-first-node-for-network"></a>Primeiro nó do cabo para rede

Esta etapa leva cerca de 10-15 minutos para ser concluída.

1. Utilize o cabo de rede RJ-45 CAT 6 para ligar o seu computador anfitrião à porta de gestão (MGMT) no dispositivo.
2. Use o cabo twinax QSFP + cobre para conectar pelo menos 1 40 Gbps (preferencial em 1 Gbps) adaptador de rede, DATA 1 ou DATA 2 para dados. Se estiver usando um comutador de 10 Gbps, use um cabo de Cobre Twinax SFP + Copper com um adaptador QSFP + to SFP + (o adaptador QSA) para conectar a interface de rede 40-Gbps para dados.
3. Instale os cabos do dispositivo conforme mostrado abaixo.  

    ![Data Box Heavy cabeado](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Configurar o primeiro nó

Este passo demora entre 5 a 7 minutos.

1. Para obter a palavra-passe do dispositivo, aceda a **Geral > Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com). A mesma senha é usada para ambos os nós do dispositivo.
2. Atribua um endereço IP estático de 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que você está usando para se conectar ao Data Box Heavy. Aceda à IU da Web local do dispositivo em `https://192.168.100.10`. A ligação pode demorar até 5 minutos depois de ligar o dispositivo.
3. Inicie sessão com a palavra-passe do portal do Azure. Verá um erro que indica um problema com o certificado de segurança do site. Siga as instruções específicas do browser para avançar para a página Web.
4. Por padrão, as configurações de rede para as interfaces (exceto o MGMT) são configuradas como DHCP. Se necessário, você pode configurar essas interfaces como estáticas e fornecer um endereço IP.

## <a name="cable-and-configure-the-second-node"></a>Cabo e configurar o segundo nó

Esta etapa leva cerca de 15-20 minutos para ser concluída.

Siga as etapas usadas para o primeiro nó a cabo e configure o segundo nó no dispositivo.  


::: zone target = "docs"

## <a name="copy-data"></a>Copiar dados

O tempo para concluir essa operação depende do tamanho dos dados e da velocidade da rede sobre a qual os dados são copiados.
 
1. Copie os dados para ambos os nós de dispositivo usando as interfaces de dados 40-Gbps em paralelo.

    - Se estiver usando um host do Windows, use uma ferramenta de cópia de arquivo compatível com SMB, como o [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Para o anfitrião NFS, utilize o comando `cp` ou `rsync` para copiar os dados.
2. Conecte-se aos compartilhamentos no dispositivo usando o`\\<IP address of your device>\ShareName`caminho:. Para obter as credenciais de acesso de compartilhamento, vá para a página **conectar & Copiar** na interface do usuário da Web local do data Box Heavy.
3. Verifique se os nomes de compartilhamento e pasta e os dados seguem as diretrizes descritas nos [limites de serviço de armazenamento e data Box Heavy do Azure](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Preparar para enviar

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Depois que a cópia de dados for concluída sem erros, vá para **preparação para o envio** página na interface do usuário da Web local e inicie a preparação do envio.
2. Depois que a **preparação para o envio** for concluída com êxito em ambos os nós, desative o dispositivo da interface do usuário da Web local.

## <a name="ship-to-azure"></a>Enviar para o Azure

Esta operação leva cerca de 15-20 minutos para ser concluída.

1. Remova os cabos e devolva-os para a bandeja na parte posterior do dispositivo.
2. Agende um recebimento com sua operadora regional.
3. Entre em contato com [Data Box operações](mailto:DataBoxOps@microsoft.com) para informar sobre a retirada e para obter o rótulo de remessa de devolução.
4. O rótulo de remessa de devolução deve estar visível no painel de limpeza frontal do dispositivo.

## <a name="verify-data"></a>Verificar os dados

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Quando o dispositivo de Data Box Heavy está conectado à rede de datacenter do Azure, os dados são carregados automaticamente no Azure.
2. Data Box serviço notifica que a cópia de dados foi concluída por meio do portal do Azure.

    1. Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
    2. Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

## <a name="clean-up-resources"></a>Limpar recursos

Este passo demora entre 2 a 3 minutos.

- Você pode cancelar a ordem de Data Box Heavy no portal do Azure antes de o pedido ser processado. Assim que a encomenda estiver processada, não é possível cancelá-la. A encomenda avança até atingir a fase de conclusão. Para cancelar a encomenda, aceda a **Descrição geral** e clique em **Cancelar** na barra de comandos.

- Pode eliminar a encomenda assim que o estado for apresentado como **Concluído** ou **Cancelado** no portal do Azure. Para eliminar a encomenda, aceda a **Descrição geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você implantou um Data Box Heavy para ajudar a importar seus dados para o Azure. Para saber mais sobre o gerenciamento de Azure Data Box Heavy, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar Data Box Heavy](data-box-portal-admin.md)

::: zone-end
