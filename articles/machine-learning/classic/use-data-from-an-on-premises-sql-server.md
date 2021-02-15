---
title: 'ML Studio (clássico): No local SQL Server - Azure'
description: Utilize dados de uma base de dados do SQL Server para realizar análises avançadas com o Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517404"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>Execute análises com o Azure Machine Learning Studio (clássico) usando uma base de dados sql server

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Muitas vezes, as empresas que trabalham com dados no local gostariam de aproveitar a escala e a agilidade da nuvem para as suas cargas de trabalho de aprendizagem automática. Mas não querem perturbar os seus processos de negócio e fluxos de trabalho atuais, movendo os seus dados no local para a nuvem. O Azure Machine Learning Studio (clássico) suporta agora a leitura dos seus dados a partir de uma base de dados do SQL Server e, em seguida, treinar e marcar um modelo com estes dados. Já não é preciso copiar manualmente e sincronizar os dados entre a nuvem e o servidor no local. Em vez disso, o módulo **de Dados de Importação** no Azure Machine Learning Studio (clássico) pode agora ler diretamente a partir da sua base de dados SQL Server para os seus trabalhos de formação e pontuação.

Este artigo fornece uma visão geral de como ingressar os dados do SQL Server no Azure Machine Learning Studio (clássico). Assume que está familiarizado com conceitos de Estúdio (clássicos) como espaços de trabalho, módulos, conjuntos de dados, experiências, *etc.*

> [!NOTE]
> Esta funcionalidade não está disponível para espaços de trabalho gratuitos. Para obter mais informações sobre preços e níveis de machine learning, consulte [o Azure Machine Learning Pricing](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instale o tempo de execução de integração auto-hospedado na Fábrica de Dados
Para aceder a uma base de dados do SQL Server no Azure Machine Learning Studio (clássico), é necessário descarregar e instalar o Tempo de Execução de Integração Auto-hospedado na Data Factory, anteriormente conhecido como Gateway de Gestão de Dados. Ao configurar a ligação no Machine Learning Studio (clássico), tem a oportunidade de descarregar e instalar o Tempo de Execução de Integração (IR) utilizando o diálogo **de gateway de dados de descarregamento e registo** descrito abaixo.


Também pode instalar o IR com antecedência, descarregando e executando o pacote de configuração MSI a partir do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser usado para atualizar um IR existente para a versão mais recente, com todas as configurações preservadas.

A Data Factory Self-Hosted Integration Runtime tem os seguintes pré-requisitos:

* A Integração Self-Hosted da Fábrica de Dados requer um Sistema Operativo de 64 bits com .NET Framework 4.6.1 ou superior.
* As versões suportadas do sistema operativo Windows são o Windows 10 , Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* A configuração recomendada para a máquina de infravermelhos é de pelo menos 2 GHz, 4 Core CPU, 8GB de RAM e disco de 80GB.
* Se a máquina hospedeira hibernar, o IR não responderá aos pedidos de dados. Por isso, configurar um plano de alimentação apropriado no computador antes de instalar o IR. Se a máquina estiver configurada para hibernar, a instalação IV apresenta uma mensagem.
* Como a atividade da cópia ocorre numa frequência específica, a utilização do recurso (CPU, memória) na máquina também segue o mesmo padrão com tempos de pico e marcha lenta. A utilização de recursos também depende fortemente da quantidade de dados que estão a ser movidos. Quando vários trabalhos de cópia estiverem em andamento, observará o uso de recursos subir durante os tempos de pico. Embora a configuração mínima acima seja tecnicamente suficiente, pode querer ter uma configuração com mais recursos do que a configuração mínima dependendo da sua carga específica para o movimento de dados.

Considere o seguinte ao configurar e utilizar um Tempo de Execução de Integração Auto-hospedado na Fábrica de Dados:

* Pode instalar apenas uma instância de IR num único computador.
* Pode utilizar um único IR para várias fontes de dados no local.
* Pode ligar várias IRs em diferentes computadores à mesma fonte de dados no local.
* Configura uma IR para apenas um espaço de trabalho de cada vez. Atualmente, as IRs não podem ser partilhadas em espaços de trabalho.
* Pode configurar várias IRs para um único espaço de trabalho. Por exemplo, pode querer utilizar um IR que esteja ligado às suas fontes de dados de teste durante o desenvolvimento e a uma IV de produção quando estiver pronto para operacionalizar.
* O IR não precisa de estar na mesma máquina que a fonte de dados. Mas ficar mais perto da fonte de dados reduz o tempo para a porta de entrada ligar-se à fonte de dados. Recomendamos que instale o IR numa máquina diferente daquela que alberga a fonte de dados no local para que o gateway e a fonte de dados não concorram por recursos.
* Se já tiver um IR instalado no seu computador que sirva cenários power bi ou Azure Data Factory, instale um IR separado para o Azure Machine Learning Studio (clássico) noutro computador.

  > [!NOTE]
  > Não é possível executar o tempo de execução de integração auto-hospedado na Data Factory e o Power BI Gateway no mesmo computador.
  >
  >
* Você precisa usar o Data Factory Self-hosted Integration Runtime para Azure Machine Learning Studio (clássico) mesmo que você esteja usando Azure ExpressRoute para outros dados. Deve tratar a sua fonte de dados como uma fonte de dados no local (que está por trás de uma firewall) mesmo quando utiliza o ExpressRoute. Utilize o tempo de execução de integração auto-hospedado na Fábrica de Dados para estabelecer conectividade entre machine learning e a fonte de dados.

Pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de resolução de problemas no artigo [Integration Runtime in Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingress dados da sua base de dados do SQL Server para Azure Machine Learning
Nesta passagem, irá configurar um Tempo de Integração da Fábrica de Dados Azure num espaço de trabalho de Aprendizagem de Máquinas Azure, configurar e, em seguida, ler dados a partir de uma base de dados do SQL Server.

> [!TIP]
> Antes de começar, desative o bloqueador pop-up do seu navegador para `studio.azureml.net` . Se estiver a utilizar o navegador Google Chrome, descarregue e instale um dos vários plug-ins disponíveis no Google Chrome WebStore [Click Once App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory Self-hosted Integration Runtime era anteriormente conhecido como Data Management Gateway. O tutorial passo a passo continuará a referir-se a ele como uma porta de entrada.  

### <a name="step-1-create-a-gateway"></a>Passo 1: Criar um portal
O primeiro passo é criar e configurar o portal para aceder à sua base de dados SQL.

1. Faça login no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net/Home/) e selecione o espaço de trabalho em que pretende trabalhar.
2. Clique na lâmina **DEFINIÇÕES** à esquerda e, em seguida, clique no separador **DATA GATEWAYS** na parte superior.
3. Clique em **NEW DATA GATEWAY** na parte inferior do ecrã.

    ![Novo Portal de Dados](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. No novo diálogo **de gateway de dados,** insira o **Nome Gateway** e adicione opcionalmente uma **Descrição**. Clique na seta no canto inferior direito para ir ao próximo passo da configuração.

    ![Insira o nome e descrição do gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. No diálogo de gateway de dados de descarregamento e registo, copie a chave de registo GATEWAY para a área de transferência.

    ![Baixar e registar porta de dados](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Se ainda não tiver descarregado e instalado o Microsoft Data Management Gateway, clique em **Baixar o gateway de gestão de dados**. Isto leva-o ao Microsoft Download Center onde pode selecionar a versão gateway de que necessita, descarregá-la e instalá-la. Pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de resolução de problemas nas secções iniciais do artigo [Mova os dados entre fontes no local e nuvem com Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Após a instalação do gateway, o Gestor de Configuração gateway de gestão de dados será aberto e o diálogo **do gateway do Registo** é apresentado. Cole a **chave de registo gateway** que copiou para a área de transferência e clique em **Registar**.
8. Se já tiver um gateway instalado, execute o Gestor de Configuração gateway de gestão de dados. Clique **na tecla Alterar,** cole a **chave de registo gateway** que copiou para a área de transferência no passo anterior e clique em **OK**.
9. Quando a instalação estiver concluída, é apresentado o diálogo **do gateway do Registo** para o Gestor de Configuração gateway de gestão de dados da Microsoft. Cole a chave de registo GATEWAY que copiou para a área de transferência num passo anterior e clique em **Registar**.

    ![Registar gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. A configuração do gateway fica completa quando os seguintes valores são definidos no separador **'Casa'** no Gestor de Configuração gateway de gestão de dados da Microsoft:

    * **O nome gateway** e **o nome de instância** estão definidos para o nome do portal.
    * **A inscrição** está marcada para **Registro.**
    * **O estado** está definido para **Iniciado**.
    * A barra de estado na parte inferior exibe **Ligação ao Serviço de Nuvem Gateway de Gestão de Dados** juntamente com uma marca de verificação verde.

      ![Gestor gateway de gestão de dados](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      O Azure Machine Learning Studio (clássico) também é atualizado quando a inscrição é bem sucedida.

    ![Registo gateway bem sucedido](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. No **diálogo de gateway de dados de descarregamento e registo,** clique na marca de verificação para completar a configuração. A página **Definições** apresenta o estado do gateway como "Online". No painel direito, encontrará o estado e outras informações úteis.

    ![Definições de gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. No Gestor de Configuração gateway de gestão de dados da Microsoft, mude para o **separador Certificado.** O certificado especificado neste separador é utilizado para encriptar/desencriptar credenciais para a loja de dados no local que especifica no portal. Este certificado é o certificado de incumprimento. A Microsoft recomenda que este seja alterado para o seu próprio certificado que faça uma retrossofação no seu sistema de gestão de certificados. Clique **em Alterar** para utilizar o seu próprio certificado.

    ![Alterar certificado de gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcional) Se pretender ativar a marcação de registo verboso de forma a resolver problemas com o gateway, no Gestor de Configuração gateway de gestão de **dados** da Microsoft, mude para o separador De diagnóstico e verifique o **registo verboso Enable para a opção de resolução de problemas.** As informações de registo podem ser **encontradas** no Visualizador de Eventos do Windows sob o nó gateway  - &gt; **de gestão de dados de** aplicações e serviços. Também pode utilizar o separador **Diagnóstico para** testar a ligação a uma fonte de dados no local utilizando o gateway.

    ![Ativar registo verboso](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Isto completa o processo de configuração do gateway no Azure Machine Learning Studio (clássico).
Está agora pronto para usar os seus dados no local.

Pode criar e configurar vários gateways em Studio (clássico) para cada espaço de trabalho. Por exemplo, pode ter um portal que pretende ligar às fontes de dados de teste durante o desenvolvimento, e uma porta de entrada diferente para as suas fontes de dados de produção. O Azure Machine Learning Studio (clássico) dá-lhe a flexibilidade para configurar vários gateways dependendo do seu ambiente corporativo. Atualmente não é possível partilhar um portal entre espaços de trabalho e apenas um portal pode ser instalado num único computador. Para obter mais informações, consulte [os dados de movimento entre fontes no local e nuvem com o Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passo 2: Utilize a porta de entrada para ler dados a partir de uma fonte de dados no local
Depois de configurar o gateway, pode adicionar um módulo **de Dados de Importação** a uma experiência que introduz os dados a partir da base de dados do SQL Server.

1. No Machine Learning Studio (clássico), selecione o **separador EXPERIMENTS,** clique em **+NEW** no canto inferior esquerdo e selecione **Blank Experiment** (ou selecione uma das várias experiências de amostra disponíveis).
2. Encontre e arraste o módulo **de Dados de Importação** para a tela de experiência.
3. Clique **em Guardar como** abaixo da tela. Introduza "Azure Machine Learning Studio (clássico) On-Premises SQL Server Tutorial" para o nome da experiência, selecione o espaço de trabalho e clique na marca de verificação **OK.**

   ![Salvar experiência com um novo nome](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Clique no módulo **Dados de Importação** para selecioná-lo e, em seguida, no painel **propriedades** à direita da tela, selecione "Base de Dados SQL" nas instalações da lista de desistências de fontes de **dados.**
5. Selecione o **portal data** que instalou e registou. Pode configurar outra porta de entrada selecionando "(adicionar novo Gateway de dados...)".

   ![Selecione porta de dados para módulo de dados de importação](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Introduza o nome do servidor e o nome do **base** **de dados** SQL, juntamente com a consulta **SQL Database** que pretende executar.
7. Clique **em Introduzir valores** no **nome de utilizador e na palavra-passe** e insira as credenciais de base de dados. Pode utilizar a autenticação integrada do Windows ou a autenticação do servidor SQL dependendo da configuração do seu Servidor SQL.

   ![Insira credenciais de base de dados](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   A mensagem "valores necessários" altera-se para "valores definidos" com uma marca de verificação verde. Só precisa de introduzir as credenciais uma vez, a menos que a informação da base de dados ou a palavra-passe mude. O Azure Machine Learning Studio (clássico) utiliza o certificado que forneceu quando instalou o portal para encriptar as credenciais na nuvem. O Azure nunca armazena credenciais no local sem encriptação.

   ![Propriedades do módulo de dados de importação](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Clique **em RUN** para executar a experiência.

Uma vez terminada a experiência, pode visualizar os dados que importou da base de dados clicando na porta de saída do módulo **de Dados de Importação** e selecionando o **Visualize.**

Assim que terminar de desenvolver a sua experiência, pode implantar e operacionalizar o seu modelo. Utilizando o Serviço de Execução de Lotes, os dados da base de dados do SQL Server configurados no módulo **de Dados de Importação** serão lidos e utilizados para a pontuação. Embora possa utilizar o Serviço de Resposta a Pedidos para obter dados no local, a Microsoft recomenda a utilização do [Excel Add-in.](excel-add-in-for-web-services.md) Atualmente, escrever para uma base de dados do SQL Server através **de Dados de Exportação** não é suportado nem nas suas experiências nem nos serviços web publicados.