---
title: SQL Server local
titleSuffix: ML Studio (classic) Azure
description: Use dados de um banco de dado SQL Server local para executar análises avançadas com a versão clássica do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 7f525a2a81d6cda52b363d1fb808327c9d3c1fb1
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622090"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Executar análise com Azure Machine Learning Studio (clássico) usando um banco de dados SQL Server local

Muitas vezes, as empresas que trabalham com dados locais gostariam de aproveitar a escala e a agilidade da nuvem para suas cargas de trabalho de aprendizado de máquina. Mas eles não querem interromper seus processos de negócios e fluxos de trabalho atuais movendo seus dados locais para a nuvem. Azure Machine Learning Studio (clássico) agora dá suporte à leitura de seus dados de um banco de SQL Server local e, em seguida, ao treinamento e à pontuação de um modelo com esses dados. Você não precisa mais copiar e sincronizar manualmente os dados entre a nuvem e o servidor local. Em vez disso, o módulo **importar dados** na versão clássica do Azure Machine Learning Studio pode agora ler diretamente do banco de SQL Server local para seus trabalhos de treinamento e pontuação.

Este artigo fornece uma visão geral de como ingressar dados do SQL Server local na versão clássica do Azure Machine Learning Studio. Ele pressupõe que você esteja familiarizado com a versão clássica dos conceitos do estúdio, como espaços de trabalho, módulos, conjuntos de testes, experimentos, *etc.* .

> [!NOTE]
> Este recurso não está disponível para espaços de trabalho gratuitos. Para obter mais informações sobre preços e camadas do Machine Learning, consulte [preços de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalar o Integration Runtime de Data Factory auto-hospedados
Para acessar um banco de dados SQL Server local na versão clássica do Azure Machine Learning Studio, você precisa baixar e instalar o Integration Runtime de Data Factory auto-hospedado, anteriormente conhecido como gateway de Gerenciamento de Dados. Ao configurar a conexão no Machine Learning Studio (clássico), você tem a oportunidade de baixar e instalar o Integration Runtime (IR) usando a caixa de diálogo **baixar e registrar o gateway de dados** descrita abaixo.


Você também pode instalar o IR antes do tempo baixando e executando o pacote de instalação do MSI do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar um IR existente para a versão mais recente, com todas as configurações preservadas.

O Integration Runtime de Data Factory auto-hospedado tem os seguintes pré-requisitos:

* O Data Factory integração auto-hospedado requer um sistema operacional de 64 bits com .NET Framework 4.6.1 ou superior.
* As versões do sistema operacional Windows com suporte são Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* A configuração recomendada para o computador IR é pelo menos 2 GHz, 4 núcleos de CPU, 8 GB de RAM e disco de 80GB.
* Se a máquina de anfitrião hiberna, o runtime de integração não responde a pedidos de dados. Portanto, configure um plano de energia apropriado no computador antes de instalar o IR. Se o computador estiver configurado para hibernar, a instalação do IR exibirá uma mensagem.
* Como a atividade de cópia ocorre em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com horários de pico e ocioso. A utilização de recursos também depende muito da quantidade de dados que estão sendo movidos. Quando vários trabalhos de cópia estiverem em andamento, você observará que o uso do recurso é acumulado durante horários de pico. Embora a configuração mínima listada acima seja tecnicamente suficiente, talvez você queira ter uma configuração com mais recursos do que a configuração mínima, dependendo de sua carga específica para a movimentação de dados.

Considere o seguinte ao configurar e usar um Integration Runtime Data Factory auto-hospedado:

* Você pode instalar apenas uma instância do IR em um único computador.
* Você pode usar um único IR para várias fontes de dados locais.
* Você pode conectar vários IRs em computadores diferentes à mesma fonte de dados local.
* Você configura um IRs para apenas um espaço de trabalho por vez. Atualmente, o IRs não pode ser compartilhado entre espaços de trabalho.
* Você pode configurar vários IRs para um único espaço de trabalho. Por exemplo, talvez você queira usar um IR conectado às suas fontes de dados de teste durante o desenvolvimento e um IR de produção quando estiver pronto para colocar em operação.
* O IR não precisa estar no mesmo computador que a fonte de dados. Mas ficar mais próximo da fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o IR em um computador diferente daquele que hospeda a fonte de dados local para que o gateway e a fonte de dados não concorram por recursos.
* Se você já tiver um IR instalado no computador que atende Power BI ou Azure Data Factory cenários, instale um IR separado para a versão clássica do Azure Machine Learning Studio em outro computador.

  > [!NOTE]
  > Não é possível executar Data Factory Integration Runtime auto-hospedado e Power BI Gateway no mesmo computador.
  >
  >
* Você precisa usar o Integration Runtime de Data Factory auto-hospedado para a versão clássica do Azure Machine Learning Studio mesmo que esteja usando o Azure ExpressRoute para outros dados. Você deve tratar sua fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando você usa o ExpressRoute. Use o Integration Runtime Data Factory auto-hospedado para estabelecer a conectividade entre Machine Learning e a fonte de dados.

Você pode encontrar informações detalhadas sobre os pré-requisitos de instalação, as etapas de instalação e as dicas de solução de problemas no artigo [Integration Runtime em data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Entrada de dados do seu banco de SQL Server local no Azure Machine Learning
Neste tutorial, você configurará um Azure Data Factory Integration Runtime em um espaço de trabalho Azure Machine Learning, configurá-lo e, em seguida, ler dados de um banco de SQL Server local.

> [!TIP]
> Antes de começar, desabilite o bloqueador de pop-ups do navegador para `studio.azureml.net`. Se você estiver usando o navegador Google Chrome, baixe e instale um dos vários plug-ins disponíveis no Google Chrome webstore [clique em extensão de aplicativo](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory Integration Runtime auto-hospedado anteriormente era conhecido como gateway Gerenciamento de Dados. O tutorial passo a passo continuará a se referir a ele como um gateway.  

### <a name="step-1-create-a-gateway"></a>Etapa 1: criar um gateway
A primeira etapa é criar e configurar o gateway para acessar seu banco de dados SQL local.

1. Faça logon no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net/Home/) e selecione o espaço de trabalho no qual você deseja trabalhar.
2. Clique na folha **configurações** à esquerda e, em seguida, clique na guia **gateways de dados** na parte superior.
3. Clique em **novo gateway de dados** na parte inferior da tela.

    ![Novo gateway de dados](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Na caixa de diálogo **novo gateway de dados** , insira o **nome do gateway** e, opcionalmente, adicione uma **Descrição**. Clique na seta no canto inferior direito para ir para a próxima etapa da configuração.

    ![Insira o nome e a descrição do gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Na caixa de diálogo baixar e registrar o gateway de dados, copie a chave de registro do GATEWAY para a área de transferência.

    ![Baixar e registrar o gateway de dados](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Se você ainda não baixou e instalou o gateway do Microsoft Gerenciamento de Dados, clique em **baixar o gateway de gerenciamento de dados**. Isso levará você ao centro de download da Microsoft, no qual você pode selecionar a versão de gateway de que precisa, baixá-lo e instalá-lo. Você pode encontrar informações detalhadas sobre os pré-requisitos de instalação, as etapas de instalação e as dicas de solução de problemas nas seções iniciais do artigo [mover dados entre fontes locais e a nuvem com gerenciamento de dados gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Depois que o gateway for instalado, o Gerenciamento de Dados gateway Configuration Manager será aberto e a caixa de diálogo **registrar gateway** será exibida. Cole a **chave de registro do gateway** que você copiou para a área de transferência e clique em **registrar**.
8. Se você já tiver um gateway instalado, execute o Gerenciamento de Dados gateway Configuration Manager. Clique em **alterar chave**, Cole a **chave de registro do gateway** que você copiou para a área de transferência na etapa anterior e clique em **OK**.
9. Quando a instalação for concluída, a caixa de diálogo **registrar gateway** para o Microsoft Gerenciamento de Dados gateway Configuration Manager será exibida. Cole a chave de registro do GATEWAY que você copiou para a área de transferência em uma etapa anterior e clique em **registrar**.

    ![Registrar gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. A configuração do gateway é concluída quando os seguintes valores são definidos na guia **página inicial** do Microsoft Gerenciamento de Dados gateway Configuration Manager:

    * O nome do **Gateway** e o **nome da instância** são definidos como o nome do gateway.
    * O **registro** está definido como **registrado**.
    * **Status** é definido como **iniciado**.
    * A barra de status na parte inferior exibe **conectado a gerenciamento de dados serviço de nuvem do gateway** junto com uma marca de seleção verde.

      ![Gerenciador de gateway Gerenciamento de Dados](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      A versão clássica do Azure Machine Learning Studio também é atualizada quando o registro é bem-sucedido.

    ![Registro do gateway bem-sucedido](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Na caixa de diálogo **baixar e registrar o gateway de dados** , clique na marca de seleção para concluir a instalação. A página **configurações** exibe o status do gateway como "online". No painel à direita, você encontrará o status e outras informações úteis.

    ![Configurações do gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. No gateway do Microsoft Gerenciamento de Dados Configuration Manager alterne para a guia **certificado** . O certificado especificado nessa guia é usado para criptografar/descriptografar credenciais para o armazenamento de dados local que você especificar no Portal. Esse certificado é o certificado padrão. A Microsoft recomenda alterar isso para seu próprio certificado que você faz backup em seu sistema de gerenciamento de certificados. Clique em **alterar** para usar seu próprio certificado.

    ![Alterar certificado de gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. adicional Se você quiser habilitar o log detalhado para solucionar problemas com o gateway, na Configuration Manager do gateway do Microsoft Gerenciamento de Dados alterne para a guia **diagnóstico** e marque a opção **habilitar log detalhado para fins de solução de problemas** opção. As informações de registro em log podem ser encontradas na Visualizador de Eventos do Windows nos **logs de aplicativos e serviços** -&gt; gerenciamento de dados nó de **Gateway** . Você também pode usar a guia **diagnóstico** para testar a conexão com uma fonte de dados local usando o gateway.

    ![Habilitar log detalhado](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Isso conclui o processo de instalação do gateway na versão clássica do Azure Machine Learning Studio.
Agora você está pronto para usar seus dados locais.

Você pode criar e configurar vários gateways no Studio (clássico) para cada espaço de trabalho. Por exemplo, você pode ter um gateway que deseja conectar às suas fontes de dados de teste durante o desenvolvimento e um gateway diferente para suas fontes de dados de produção. A versão clássica do Azure Machine Learning Studio oferece a flexibilidade de configurar vários gateways, dependendo do ambiente corporativo. No momento, você não pode compartilhar um gateway entre espaços de trabalho e apenas um gateway pode ser instalado em um único computador. Para obter mais informações, consulte [mover dados entre fontes locais e nuvem com gerenciamento de dados gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Etapa 2: usar o gateway para ler dados de uma fonte de dados local
Depois de configurar o gateway, você pode adicionar um módulo **importar dados** a um experimento que insere os dados do banco de SQL Server local.

1. Em Machine Learning Studio (clássico), selecione a guia **experimentos** , clique em **+ novo** no canto inferior esquerdo e selecione **experimento em branco** (ou selecione uma das várias experiências de exemplo disponíveis).
2. Localize e arraste o módulo **importar dados** para a tela do experimento.
3. Clique em **salvar como** abaixo da tela. Insira o tutorial de SQL Server local "Azure Machine Learning Studio (clássico)" para o nome do experimento, selecione o espaço de trabalho e clique na marca de seleção **OK** .

   ![Salvar experimento com um novo nome](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Clique no módulo **importar dados** para selecioná-lo e, em seguida, no painel **Propriedades** à direita da tela, selecione "SQL Database local" na lista suspensa **fonte de dados** .
5. Selecione o **Gateway de dados** que você instalou e registrou. Você pode configurar outro gateway selecionando "(Adicionar novo gateway de dados...)".

   ![Selecionar o gateway de dados para o módulo importar dados](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Insira o **nome do servidor do banco de dados** SQL e o nome do **banco**de dados, juntamente com a **consulta do banco** de dados SQL que você deseja executar.
7. Clique em **Inserir valores** em **nome de usuário e senha** e insira suas credenciais de banco de dados. Você pode usar a autenticação integrada do Windows ou a autenticação SQL Server dependendo de como sua SQL Server local está configurada.

   ![Inserir credenciais de banco de dados](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   A mensagem "valores necessários" muda para "valores definidos" com uma marca de seleção verde. Você só precisa inserir as credenciais uma vez, a menos que as informações do banco de dados ou a senha sejam alteradas. A versão clássica do Azure Machine Learning Studio usa o certificado fornecido quando você instalou o gateway para criptografar as credenciais na nuvem. O Azure nunca armazena credenciais locais sem criptografia.

   ![Importar propriedades do módulo de dados](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Clique em **executar** para executar o experimento.

Depois que o experimento terminar a execução, você poderá visualizar os dados importados do banco de dados, clicando na porta de saída do módulo **importar data** e selecionando **Visualizar**.

Depois de concluir o desenvolvimento do experimento, você poderá implantar e colocar em operação seu modelo. Usando o serviço de execução em lotes, os dados do banco de SQL Server local configurado no módulo **importar dados** serão lidos e usados para pontuação. Embora você possa usar o serviço de resposta de solicitação para Pontuação de dados locais, a Microsoft recomenda usar o [suplemento do Excel](excel-add-in-for-web-services.md) em vez disso. No momento, não há suporte para a gravação em um banco de dados local SQL Server por meio de **Export data** em seus experimentos ou serviços Web publicados.
