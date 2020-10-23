---
title: 'Início Rápido: exportar dados para o Microsoft Azure Data Box'
description: Saiba como exportar rapidamente os dados do Azure Data Box no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 789bd2f62673e7faf562d3a407bf2f0a4fd861bf
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125497"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>Início Rápido: introdução ao Azure Data Box para exportar dados do Azure

Este início rápido descreve como exportar dados do Azure para a sua localização através do portal do Azure. Os passos incluem como instalar os cabos, configurar e copiar dados do Azure. O início rápido é efetuado no portal do Azure e na IU da Web local do dispositivo.

Para uma implementação passo a passo detalhada e instruções de controlo, aceda ao [Tutorial: criar ordem de exportação para o Azure Data Box](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
  * Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
  * Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](/azure/cloud-solution-provider/overview/azure-csp-overview).
  * Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

* Certifique-se de que tem acesso de proprietário ou de contribuinte à subscrição para criar uma encomenda do Data Box.
* Reveja as [diretrizes de segurança para o Data Box](data-box-safety.md).
* Tem um dispositivo Azure Data Box para mover dados para a sua localização. O computador anfitrião tem de
  * Executar um [sistema operativo suportado](data-box-system-requirements.md).
  * Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, poderá ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.
* Tem acesso a uma superfície plana onde possa colocar o Data Box. Se pretende colocar o dispositivo numa prateleira de rack padrão, precisará de um bloco de 7U no rack do datacenter. Pode colocar o dispositivo na horizontal ou vertical no rack.
* Obteve os cabos seguintes para ligar o Data Box ao computador anfitrião.
  * Dois cabos de cobre SFP + Twinax de 10 GbE (utilizar com interfaces de rede DATA 1, DATA 2)
  * Um cabo de rede RJ-45 CAT 6 (utilizar com interface de rede de gestão (MGMT))
  * Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (utilizar com a interface de rede DADOS 3, configurada como 10 Gbps ou 1 Gbps, respetivamente)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="order"></a>Encomenda

Este passo demora aproximadamente 10 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure.
2. Selecione uma subscrição existente ativada para este serviço e escolha o tipo de transferência, como **Exportar para o Azure**. Indique a **Região do Azure de origem** onde os dados residem e o **País de destino** para a transferência de dados.
3. Selecione **Data Box**. A capacidade máxima utilizável é de 80 TB e pode criar várias encomendas para tamanhos de dados superiores.
4. Selecione **Adicionar conta de armazenamento e tipo de exportação** e, em seguida, **Selecionar opção de exportação**.
5. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, o dispositivo é preparado para envio.

## <a name="cable"></a>Instalar os cabos

Este passo demora aproximadamente 10 minutos.

Quando receber o Data Box, siga os passos abaixo para instalar os cabos, ligar e ativar o dispositivo. Este passo demora cerca de 10 minutos.

1. Se existirem quaisquer evidências de que o dispositivo foi adulterado ou está danificado, não prossiga. Contacte o Suporte da Microsoft para enviar-lhe um dispositivo de substituição.
2. Antes de instalar os cabos do dispositivo, certifique-se de que tem os cabos seguintes:

   * Cabo de alimentação com terra (incluído), com a classificação 10 A ou superior, com um conector IEC60320 C-13 numa extremidade para ligar ao dispositivo.
   * Um cabo de rede RJ-45 CAT 6 (utilizar com interface de rede de gestão (MGMT))
   * Dois cabos de cobre SFP + Twinax de 10 GbE (utilizar com interfaces de rede DADOS 1, DADOS 2 de 10 Gbps)
   * Um cabo de rede RJ-45 CAT 6A OU RJ-45 CAT 6 (utilizar com a interface de rede DADOS 3, configurada como 10 Gbps ou 1 Gbps, respetivamente)

3. Remova e coloque o dispositivo numa superfície plana.

4. Instale os cabos do dispositivo conforme mostrado abaixo.  

    ![Plano posterior do dispositivo Data Box com os cabos instalados](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. Ligue o cabo de alimentação ao dispositivo.
    2. Utilize o cabo de rede RJ-45 CAT 6 para ligar o computador anfitrião à porta MGMT no dispositivo.
    3. Utilize o cabo de cobre SFP+ Twinax para ligar, uma interface de rede de, pelo menos, 10 Gbps (preferível face a 1 Gbps), DATA 1 ou DATA 2 para dados.
    4. Ligue o dispositivo. O botão de energia está no painel frontal do dispositivo.

## <a name="connect"></a>Ligar

Este passo demora entre 5 a 7 minutos.

1. Para obter a palavra-passe do dispositivo, aceda a **Geral** > **Detalhes do dispositivo** no [portal do Azure](https://portal.azure.com).
2. Atribua um endereço IP estático 192.168.100.5 e a sub-rede 255.255.255.0 ao adaptador Ethernet no computador que está a utilizar para ligar ao Data Box. Aceda à IU da Web local do dispositivo em `https://192.168.100.10`. A ligação pode demorar até 5 minutos depois de ligar o dispositivo.
3. Inicie sessão com a palavra-passe do portal do Azure. Verá um erro que indica um problema com o certificado de segurança do site. Siga as instruções específicas do browser para avançar para a página Web.
4. Por predefinição, as definições de rede da interface de dados de 10 Gbps (ou de 1 Gbps) estão configuradas como DHCP. Se for necessário, pode configurar esta interface como estática e fornecer um endereço IP.

## <a name="copy-data"></a>Copiar dados

O tempo de conclusão desta operação depende do tamanho dos dados e da velocidade da rede.

1. Se estiver a utilizar um sistema anfitrião do Windows, utilize uma ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy. Para o anfitrião NFS, utilize o comando `cp` ou `rsync` para copiar os dados. Para obter mais informações sobre como utilizar o Robocopy para copiar dados, aceda a [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10)).
2. Ligue-se às partilhas de dispositivos e comece a copiar os dados para o computador anfitrião.
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>Enviar para o Azure

Esta operação demora entre 10 a 15 minutos.

1. Aceda à página **Preparar para enviar** na IU da Web local e comece a preparação do envio.
2. Desative o dispositivo a partir da IU da Web local. Remova os cabos do dispositivo.
3. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
4. A etiqueta de devolução deve estar visível no ecrã de E-ink. Se o E-ink não apresentar a etiqueta, aceda a **Descrição geral** > **Transferir etiqueta de envio** no portal do Azure. Transfira a guia de remessa e cole-a no dispositivo.
5. Agende uma recolha com a transportadora regional caso queira devolver o dispositivo.
6. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

## <a name="clean-up-resources"></a>Limpar recursos

Este passo demora entre 2 a 3 minutos.

* Pode cancelar a encomenda do Data Box no portal do Azure antes de ser processada. Não é possível cancelar a encomenda após o seu processamento. A encomenda avança até atingir a fase de conclusão. Para cancelar a encomenda, aceda a **Descrição geral** e selecione **Cancelar** na barra de comando.

* Pode eliminar a encomenda assim que o estado for apresentado como **Concluído** ou **Cancelado** no portal do Azure. Para eliminar a encomenda, aceda a **Descrição geral** e selecione **Eliminar** na barra de comando.

## <a name="next-steps"></a>Próximos passos

Neste início rápido, implementou uma ordem de exportação do Azure para um Azure Data Box. Para saber mais sobre a gestão do Azure Data Box, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Data Box](data-box-portal-admin.md)