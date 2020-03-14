---
title: SQL Server no local
titleSuffix: ML Studio (classic) - Azure
description: Utilize dados de uma base de dados do SQL Server no local para realizar análises avançadas com o Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204187"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Execute análises com o Azure Machine Learning Studio (clássico) utilizando uma base de dados do SQL Server no local

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Muitas vezes, as empresas que funcionam com dados no local pretende tirar partido do dimensionamento e agilidade da cloud para as suas cargas de trabalho de aprendizagem. Mas não quer interromper os processos empresariais atuais e fluxos de trabalho ao mover seus dados no local para a cloud. O Azure Machine Learning Studio (clássico) suporta agora a leitura dos seus dados a partir de uma base de dados do SQL Server no local e, em seguida, treina ndo e marcando um modelo com estes dados. Já não terá de copiar e sincronizar os dados entre a cloud e o servidor no local manualmente. Em vez disso, o módulo **de dados de importação** no Azure Machine Learning Studio (clássico) pode agora ler diretamente a partir da sua base de dados sQL Server no local para os seus trabalhos de formação e pontuação.

Este artigo fornece uma visão geral de como ingressar dados do servidor SQL no Azure Machine Learning Studio (clássico). Assume que está familiarizado com conceitos de Estúdio (clássicos) como espaços de trabalho, módulos, conjuntos de dados, experiências, *etc.*

> [!NOTE]
> Esta funcionalidade não está disponível para áreas de trabalho gratuitas. Para obter mais informações sobre preços e níveis de Machine Learning, consulte [o Preço de Aprendizagem automática de Azure](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalar o Runtime de integração autoalojado de fábrica de dados
Para aceder a uma base de dados do SQL Server no Azure Machine Learning Studio (clássico), precisa de descarregar e instalar o Data Factory Self-Hosted Integration Runtime, anteriormente conhecido como Gateway de Gestão de Dados. Quando configurar a ligação no Machine Learning Studio (clássico), tem a oportunidade de descarregar e instalar o Tempo de Integração (IR) utilizando o diálogo de **gateway de descarregamento e registo** de dados descrito abaixo.


Também pode instalar o IR com antecedência, descarregando e executando o pacote de configuração MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar um IR existente para a versão mais recente, com todas as configurações preservadas.

O Runtime de integração de autoalojado de fábrica de dados tem os seguintes pré-requisitos:

* A integração de autoalojado da fábrica de dados requer um sistema de operativo de 64 bits com o .NET Framework 4.6.1 ou superior.
* As versões de sistema operativo do Windows suportadas são o Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* A configuração recomendada para a máquina de Runtime de integração é, pelo menos, 2 GHz, 4 núcleos de CPU, 8GB de RAM e disco de 80GB.
* Se a máquina hospedeira hibernar, o IR não responderá aos pedidos de dados. Por conseguinte, configurar uma esquema de energia adequado no computador antes de instalar o IR. Se a máquina estiver configurada para hibernação, a instalação do Runtime de integração apresenta uma mensagem.
* Porque a atividade de cópia ocorre com uma frequência específica, a utilização de recursos (CPU, memória) na máquina também segue o mesmo padrão com horas de pico e tempos de inatividade. Utilização de recursos também depende muito a quantidade de dados a ser movidos. Quando várias tarefas de cópia em curso, observará subir durante as horas de pico de utilização de recursos. Enquanto a configuração mínima listada acima é tecnicamente suficiente, poderá ter uma configuração com mais recursos do que a configuração mínima, dependendo da sua carga específico para movimento de dados.

Considere o seguinte quando configurar e utilizar o Data Factory Integration Runtime autoalojado:

* Pode instalar apenas uma instância do Runtime de integração num único computador.
* Pode usar um único Runtime de integração para várias origens de dados no local.
* Pode ligar várias IRs em computadores diferentes para a mesma origem de dados no local.
* Configurar uma identificação para apenas um espaço de trabalho de cada vez. Atualmente, as IRs não podem ser partilhadas em espaços de trabalho.
* Pode configurar vários IRs para um único espaço de trabalho. Por exemplo, pode querer utilizar um IR que esteja ligado às fontes de dados do seu teste durante o desenvolvimento e um IR de produção quando estiver pronto para operacionalizar.
* O runtime de integração não precisa de estar no mesmo computador, como a origem de dados. Mas, mantendo-se mais de perto para a origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o runtime de integração num computador que é diferente do que aloja a origem de dados no local para que a origem de dados e de gateway não compitam por recursos.
* Se já tiver um IR instalado no seu computador a servir cenários power BI ou Azure Data Factory, instale um IR separado para o Azure Machine Learning Studio (clássico) noutro computador.

  > [!NOTE]
  > Não é possível executar o Runtime de integração de autoalojado do Data Factory e Power BI Gateway no mesmo computador.
  >
  >
* Você precisa usar o Tempo de Integração Auto-hospedado da Fábrica de Dados para o Azure Machine Learning Studio (clássico) mesmo que esteja a usar o Azure ExpressRoute para outros dados. Deve tratar sua origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando utilizar o ExpressRoute. Utilize o Runtime de integração de autoalojado de fábrica de dados para estabelecer a conectividade entre a origem de dados e o Machine Learning.

Pode encontrar informações detalhadas sobre os pré-requisitos de instalação, os passos de instalação e dicas de resolução de problemas no artigo [Integração Runtime in Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingressando dados da sua base de dados do SQL Server no local em Azure Machine Learning
Neste walkthrough, você vai configurar um Tempo de Integração de Fábrica de Dados Azure em um espaço de trabalho de Aprendizagem automática Azure, configurá-lo e, em seguida, ler dados de uma base de dados do SQL Server no local.

> [!TIP]
> Antes de começar, desative o bloqueador pop-up do seu navegador para `studio.azureml.net`. Se estiver a utilizar o navegador Google Chrome, descarregue e instale um dos vários plug-ins disponíveis no Google Chrome WebStore [Click Once App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> O Azure Data Factory Integration Runtime autoalojado era anteriormente conhecido como Data Management Gateway. O tutorial passo a passo irá continuar a fazer referência a ele como um gateway.  

### <a name="step-1-create-a-gateway"></a>Passo 1: Criar um gateway
A primeira etapa é criar e configurar o gateway para aceder à sua base de dados do SQL no local.

1. Inicie sessão no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net/Home/) e selecione o espaço de trabalho em que pretende trabalhar.
2. Clique na lâmina **DEFINIÇÕES** à esquerda e, em seguida, clique no separador **DATA GATEWAYS** na parte superior.
3. Clique em **NOVO DATA GATEWAY** na parte inferior do ecrã.

    ![Novo Gateway de dados](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. No diálogo de gateway de **dados novos,** introduza o **Nome Gateway** e adicione opcionalmente uma **Descrição**. Clique na seta no canto inferior direito para ir para a próxima etapa da configuração.

    ![Introduza o nome de gateway e uma descrição](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. O Download e registre-se dados gateway caixa de diálogo, copie a chave de registo do GATEWAY para a área de transferência.

    ![Transferir e registar o gateway de dados](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Se ainda não descarregou e instalou o Gateway de Gestão de Dados da Microsoft, clique em Baixar o gateway de **gestão**de dados . Isto leva-o para a Microsoft Download Center onde pode selecionar a versão do gateway que precisa, transferi-lo e instalá-lo. Pode encontrar informações detalhadas sobre os pré-requisitos de instalação, as etapas de instalação e dicas de resolução de problemas nas secções iniciantes do artigo [Mover dados entre fontes no local e cloud com](../../data-factory/tutorial-hybrid-copy-portal.md)Data Management Gateway .
7. Após a instalação do gateway, o Gestor de Configuração de Gateway de Gestão de Dados abrirá e o diálogo de **gateway Register** é apresentado. Colar a chave de **registo** gateway que copiou para a área de redação e clicar **em Registar**.
8. Se já tiver um gateway instalado, execute o Gestor de configuração de Gateway de gestão de dados. Clique na **tecla Alterar,** colar a chave de registo do **gateway** que copiou para a área de recortes no passo anterior e clique em **OK**.
9. Quando a instalação estiver concluída, é apresentado o diálogo de **gateway Register** para o Microsoft Data Management Gateway Configuration Manager. Colar a CHAVE DE REGISTO GATEWAY que copiou para a prancheta num passo anterior e clique em **Registar**.

    ![Registar gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. A configuração do gateway fica completa quando os seguintes valores são definidos no separador **Home** no Microsoft Data Management Gateway Configuration Manager:

    * **O nome** do gateway e o **nome instance** estão definidos para o nome do portal.
    * **As inscrições** estão definidas para **O Registo.**
    * **O estado** está definido para **Iniciar**.
    * A barra de estado na parte inferior exibe **Connected to Data Management Gateway Cloud Service** juntamente com uma marca de verificação verde.

      ![Gestor de Gateway de gestão de dados](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      O Azure Machine Learning Studio (clássico) também é atualizado quando o registo é bem sucedido.

    ![Registo do ATA gateway com êxito](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. No diálogo de porta de **entrada de descarregamento e registo,** clique na marca de verificação para completar a configuração. A página **Definições** apresenta o estado do gateway como "Online". No painel da direita, encontrará estado e outras informações úteis.

    ![Definições do gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. No Microsoft Data Management Gateway Configuration Manager altere para o separador **Certificate.** O certificado especificado neste separador é utilizado para encriptar/desencriptar credenciais para a loja de dados no local que especifica no portal. Este certificado é o certificado predefinido. A Microsoft recomenda alterar isso para seu próprio certificado que criar cópias de segurança no seu sistema de gestão de certificado. Clique em **Alterar** para utilizar o seu próprio certificado.

    ![Alterar o certificado de gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcional) Se pretender ativar o registo verbose para resolver problemas com o gateway, no microsoft Data Management Gateway Configuration Manager switch para o separador **Diagnostics** e verificar a **marca de registo verbose Enable para fins** de resolução de problemas. As informações de registo podem ser encontradas no Windows Event Viewer no âmbito dos Registos de **Aplicações e Serviços** -&gt; nó gateway de gestão de **dados.** Também pode utilizar o separador **Diagnósticos** para testar a ligação a uma fonte de dados no local utilizando o gateway.

    ![Ativar o registo verboso](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Isto completa o processo de configuração de gateway no Azure Machine Learning Studio (clássico).
Agora, está pronto para utilizar os seus dados no local.

Você pode criar e configurar vários gateways em Studio (clássico) para cada espaço de trabalho. Por exemplo, pode ter um gateway que pretende ligar às suas origens de dados de teste durante o desenvolvimento e um gateway diferente para as origens de dados de produção. O Azure Machine Learning Studio (clássico) dá-lhe a flexibilidade para configurar vários gateways dependendo do seu ambiente corporativo. Atualmente não pode partilhar uma porta de entrada entre espaços de trabalho e apenas um portal pode ser instalado num único computador. Para mais informações, consulte [Mover dados entre fontes no local e cloud com Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passo 2: Utilizar o gateway para ler dados a partir de uma origem de dados no local
Depois de configurar o gateway, pode adicionar um módulo de **Dados de Importação** a uma experiência que insere os dados a partir da base de dados do SQL Server no local.

1. No Machine Learning Studio (clássico), selecione o separador **EXPERIMENTS,** clique em **+NEW** no canto inferior esquerdo e selecione **Blank Experiment** (ou selecione uma das várias experiências de amostra disponíveis).
2. Encontre e arraste o módulo **de dados de importação** para a tela de experiência.
3. Clique em **Guardar como** abaixo da tela. Introduza "Azure Machine Learning Studio (clássico) On-Premises SQL Server Tutorial" para o nome da experiência, selecione o espaço de trabalho e clique na marca de verificação **OK.**

   ![Guardar a experimentação com um novo nome](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Clique no módulo **dados de importação** para selecioná-lo e, em seguida, no painel **Properties** à direita da tela, selecione "On-Premises SQL Database" na lista de desfocagem da fonte **de dados.**
5. Selecione o **portal Data** que instalou e registou. Pode configurar outro gateway ao selecionar "(adicionar novo Gateway de dados...)".

   ![Selecione o gateway de dados para o módulo importar dados](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Introduza o **nome** do servidor da Base de Dados SQL e o nome base de **dados,** juntamente com a consulta de Base de **Dados** SQL que pretende executar.
7. Clique em **Introduzir valores** em **nome de utilizador e palavra-passe** e introduza as credenciais da sua base de dados. Pode usar a autenticação integrada do Windows ou a autenticação do SQL Server, dependendo de como o SQL Server no local está configurado.

   ![Introduza as credenciais da base de dados](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   As alterações de "valores necessários" de mensagem para "conjunto de valores" com uma marca de verificação verde. Só tem de introduzir as credenciais de uma vez, a menos que as informações de base de dados ou a palavra-passe é alterado. O Azure Machine Learning Studio (clássico) utiliza o certificado que forneceu quando instalou a porta de entrada para encriptar as credenciais na nuvem. Azure nunca armazena credenciais no local sem encriptação.

   ![Importar as propriedades do módulo de dados](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Clique em **RUN** para executar a experiência.

Uma vez que a experiência termine a execução, pode visualizar os dados importados da base de dados clicando na porta de saída do módulo **de dados** de importação e selecionando **Visualize**.

Depois de concluir a desenvolver a sua experimentação, pode implementar e operacionalizar o seu modelo. Utilizando o Serviço de Execução de Lotes, os dados da base de dados do SQL Server no local configurados no módulo **de Dados** de Importação serão lidos e utilizados para a pontuação. Embora possa utilizar o Serviço de Resposta a Pedidos para a pontuação de dados no local, a Microsoft recomenda a utilização do [Excel Add-in.](excel-add-in-for-web-services.md) Atualmente, escrever para uma base de dados do SQL Server no local através de Dados de **Exportação** não é suportado nem nas suas experiências nem serviços web publicados.
