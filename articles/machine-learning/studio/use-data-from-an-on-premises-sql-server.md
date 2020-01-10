---
title: SQL Server no local
titleSuffix: ML Studio (classic) - Azure
description: Use dados de um banco de dado SQL Server local para executar análises avançadas com Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 97ab0bd275178a080af3491ba8219d4217e233aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432212"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Executar análise com Azure Machine Learning Studio (clássico) usando um banco de dados SQL Server local

Muitas vezes, as empresas que funcionam com dados no local pretende tirar partido do dimensionamento e agilidade da cloud para as suas cargas de trabalho de aprendizagem. Mas não quer interromper os processos empresariais atuais e fluxos de trabalho ao mover seus dados no local para a cloud. Azure Machine Learning Studio (clássico) agora dá suporte à leitura de seus dados de um banco de SQL Server local e, em seguida, ao treinamento e à pontuação de um modelo com esses dados. Já não terá de copiar e sincronizar os dados entre a cloud e o servidor no local manualmente. Em vez disso, o módulo **importar dados** no Azure Machine Learning Studio (clássico) agora pode ler diretamente do banco de SQL Server local para seus trabalhos de treinamento e pontuação.

Este artigo fornece uma visão geral de como ingressar dados locais do SQL Server em Azure Machine Learning Studio (clássico). Ele pressupõe que você esteja familiarizado com os conceitos de estúdio (clássico), como espaços de trabalho, módulos, conjuntos de valores, experimentos, *etc.* .

> [!NOTE]
> Esta funcionalidade não está disponível para áreas de trabalho gratuitas. Para obter mais informações sobre preços de Machine Learning e camadas, consulte [preços do Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Instalar o Runtime de integração autoalojado de fábrica de dados
Para acessar um banco de dados SQL Server local no Azure Machine Learning Studio (clássico), você precisa baixar e instalar o Integration Runtime de Data Factory auto-hospedado, anteriormente conhecido como gateway de Gerenciamento de Dados. Ao configurar a conexão no Machine Learning Studio (clássico), você tem a oportunidade de baixar e instalar o Integration Runtime (IR) usando a caixa de diálogo **baixar e registrar o gateway de dados** descrita abaixo.


Também pode instalar o IR além do tempo ao transferir e executar o pacote de instalação do MSI do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). O MSI também pode ser utilizado para atualizar um runtime de integração existente para a versão mais recente, com todas as definições preservadas.

O Runtime de integração de autoalojado de fábrica de dados tem os seguintes pré-requisitos:

* A integração de autoalojado da fábrica de dados requer um sistema de operativo de 64 bits com o .NET Framework 4.6.1 ou superior.
* As versões de sistema operativo do Windows suportadas são o Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* A configuração recomendada para a máquina de Runtime de integração é, pelo menos, 2 GHz, 4 núcleos de CPU, 8GB de RAM e disco de 80GB.
* Se a máquina de anfitrião hiberna, o runtime de integração não responde a pedidos de dados. Por conseguinte, configurar uma esquema de energia adequado no computador antes de instalar o IR. Se a máquina estiver configurada para hibernação, a instalação do Runtime de integração apresenta uma mensagem.
* Porque a atividade de cópia ocorre com uma frequência específica, a utilização de recursos (CPU, memória) na máquina também segue o mesmo padrão com horas de pico e tempos de inatividade. Utilização de recursos também depende muito a quantidade de dados a ser movidos. Quando várias tarefas de cópia em curso, observará subir durante as horas de pico de utilização de recursos. Enquanto a configuração mínima listada acima é tecnicamente suficiente, poderá ter uma configuração com mais recursos do que a configuração mínima, dependendo da sua carga específico para movimento de dados.

Considere o seguinte quando configurar e utilizar o Data Factory Integration Runtime autoalojado:

* Pode instalar apenas uma instância do Runtime de integração num único computador.
* Pode usar um único Runtime de integração para várias origens de dados no local.
* Pode ligar várias IRs em computadores diferentes para a mesma origem de dados no local.
* Você configura um IRs para apenas um espaço de trabalho por vez. Atualmente, o IRs não pode ser compartilhado entre áreas de trabalho.
* Pode configurar vários IRs para um único espaço de trabalho. Por exemplo, talvez você queira usar um IR conectado às suas fontes de dados de teste durante o desenvolvimento e um IR de produção quando estiver pronto para colocar em operação.
* O runtime de integração não precisa de estar no mesmo computador, como a origem de dados. Mas, mantendo-se mais de perto para a origem de dados reduz o tempo para o gateway ligar à origem de dados. Recomendamos que instale o runtime de integração num computador que é diferente do que aloja a origem de dados no local para que a origem de dados e de gateway não compitam por recursos.
* Se você já tiver um IR instalado no computador que atende Power BI ou Azure Data Factory cenários, instale um IR separado para Azure Machine Learning Studio (clássico) em outro computador.

  > [!NOTE]
  > Não é possível executar o Runtime de integração de autoalojado do Data Factory e Power BI Gateway no mesmo computador.
  >
  >
* Você precisa usar o Integration Runtime de Data Factory auto-hospedado para Azure Machine Learning Studio (clássico) mesmo se estiver usando o Azure ExpressRoute para outros dados. Deve tratar sua origem de dados como uma origem de dados no local (que é protegido por uma firewall), mesmo quando utilizar o ExpressRoute. Utilize o Runtime de integração de autoalojado de fábrica de dados para estabelecer a conectividade entre a origem de dados e o Machine Learning.

Pode encontrar informações detalhadas sobre os pré-requisitos de instalação, passos de instalação e sugestões de resolução de problemas no artigo [Integration Runtime no Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dados de entrada da sua base de dados do SQL Server no local no Azure Machine Learning
Neste tutorial, você configurará um Azure Data Factory Integration Runtime em um espaço de trabalho Azure Machine Learning, configurá-lo e, em seguida, ler dados de um banco de SQL Server local.

> [!TIP]
> Antes de começar, desative o Bloqueador de pop-up de seu navegador para `studio.azureml.net`. Se estiver a utilizar o browser Google Chrome, transfira e instale um dos vários plug-ins disponíveis na loja Web do Google Chrome [clique uma vez da extensão de aplicação](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> O Azure Data Factory Integration Runtime autoalojado era anteriormente conhecido como Data Management Gateway. O tutorial passo a passo irá continuar a fazer referência a ele como um gateway.  

### <a name="step-1-create-a-gateway"></a>Passo 1: Criar um gateway
A primeira etapa é criar e configurar o gateway para aceder à sua base de dados do SQL no local.

1. Faça logon no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net/Home/) e selecione o espaço de trabalho no qual você deseja trabalhar.
2. Clique nas **definições** painel da esquerda e, em seguida, clique no **GATEWAYS de dados** separador na parte superior.
3. Clique em **novo GATEWAY de dados** na parte inferior do ecrã.

    ![Novo Gateway de dados](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Na **novo gateway de dados** caixa de diálogo, introduza o **nome do Gateway** e, opcionalmente, adicione um **Descrição**. Clique na seta no canto inferior direito para ir para a próxima etapa da configuração.

    ![Introduza o nome de gateway e uma descrição](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. O Download e registre-se dados gateway caixa de diálogo, copie a chave de registo do GATEWAY para a área de transferência.

    ![Transferir e registar o gateway de dados](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Se ainda não transferiu e instalou o Microsoft Data Management Gateway, em seguida, clique em **gateway de gestão de dados de transferência**. Isto leva-o para a Microsoft Download Center onde pode selecionar a versão do gateway que precisa, transferi-lo e instalá-lo. Pode encontrar informações detalhadas sobre os pré-requisitos de instalação, passos de instalação e sugestões de resolução de problemas nas secções de início do artigo [mover dados entre origens no local e na cloud com o Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md) .
7. Depois do gateway é instalado, o Gestor de configuração de Gateway de gestão de dados será aberta e o **Registre-se gateway** é apresentada a caixa de diálogo. Colar o **chave de registo do Gateway** que copiou para a área de transferência e clique em **registar**.
8. Se já tiver um gateway instalado, execute o Gestor de configuração de Gateway de gestão de dados. Clique em **chave de alteração**, cole a **chave de registo do Gateway** copiados para a área de transferência no passo anterior e clique em **OK**.
9. Quando a instalação estiver concluída, o **Registre-se gateway** é apresentada a caixa de diálogo para o Microsoft Data Management Gateway Configuration Manager. Cole a chave de registo do GATEWAY que copiou para a área de transferência num passo anterior e clique em **registar**.

    ![Registar gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. A configuração de gateway está concluída quando os seguintes valores estão definidos no **home page** separador no Microsoft Data Management Gateway Configuration Manager:

    * **Nome do gateway** e **nome da instância** estão definidas para o nome do gateway.
    * **Registo** está definido como **registada**.
    * **Estado** está definido como **iniciado**.
    * Barra de estado apresenta a parte inferior **ligado ao serviço Cloud do Data Management Gateway** juntamente com uma marca de verificação verde.

      ![Gestor de Gateway de gestão de dados](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (clássico) também é atualizado quando o registro é bem-sucedido.

    ![Registo do ATA gateway com êxito](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Na **transferir e registar o gateway de dados** caixa de diálogo, clique na marca de verificação para concluir a configuração. O **definições** página apresenta o estado do gateway como "Online". No painel da direita, encontrará estado e outras informações úteis.

    ![Definições do gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. No gateway do Microsoft Gerenciamento de Dados Configuration Manager alterne para a guia **certificado** . O certificado especificado nessa guia é usado para criptografar/descriptografar credenciais para o armazenamento de dados local que você especificar no Portal. Este certificado é o certificado predefinido. A Microsoft recomenda alterar isso para seu próprio certificado que criar cópias de segurança no seu sistema de gestão de certificado. Clique em **alteração** para utilizar o seu próprio certificado em vez disso.

    ![Alterar o certificado de gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (opcional) Se pretender ativar o registo verboso para resolver problemas com o gateway, no Gestor de configuração de Gateway do Microsoft Data Management mudar para o **diagnóstico** separador e verificar o **ativar verboso o registo para fins de resolução de problemas** opção. As informações de registo podem ser encontradas no Visualizador de eventos do Windows sob o **Applications and Services Logs**  - &gt; **Data Management Gateway** nó. Também pode utilizar o **diagnóstico** separador para testar a ligação a uma origem de dados no local com o gateway.

    ![Ativar o registo verboso](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Isso conclui o processo de configuração do gateway no Azure Machine Learning Studio (clássico).
Agora, está pronto para utilizar os seus dados no local.

Você pode criar e configurar vários gateways no Studio (clássico) para cada espaço de trabalho. Por exemplo, pode ter um gateway que pretende ligar às suas origens de dados de teste durante o desenvolvimento e um gateway diferente para as origens de dados de produção. Azure Machine Learning Studio (clássico) oferece a flexibilidade para configurar vários gateways, dependendo do ambiente corporativo. Atualmente, não é possível partilhar um gateway entre áreas de trabalho e apenas um gateway pode ser instalado num único computador. Para obter mais informações, consulte [mover dados entre origens no local e na cloud com o Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Passo 2: Utilizar o gateway para ler dados a partir de uma origem de dados no local
Depois de configurar o gateway, pode adicionar um **importar dados** módulo para uma experimentação que insere dados da base de dados do SQL Server no local.

1. Em Machine Learning Studio (clássico), selecione a guia **experimentos** , clique em **+ novo** no canto inferior esquerdo e selecione **experimento em branco** (ou selecione uma das várias experiências de exemplo disponíveis).
2. Localize e arraste a **importar dados** módulo para a tela da experimentação.
3. Clique em **guardar como** abaixo da tela. Insira o tutorial de SQL Server local "Azure Machine Learning Studio (clássico)" para o nome do experimento, selecione o espaço de trabalho e clique na marca de seleção **OK** .

   ![Guardar a experimentação com um novo nome](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Clique nas **importar dados** módulo selecioná-lo, em seguida, no **propriedades** painel à direita da tela, selecione "Base de dados do SQL no local" no **origem de dados** lista suspensa.
5. Selecione o **gateway de dados** instalado e registado. Pode configurar outro gateway ao selecionar "(adicionar novo Gateway de dados...)".

   ![Selecione o gateway de dados para o módulo importar dados](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Introduza o SQL **nome do servidor de base de dados** e **nome da base de dados**, juntamente com o SQL **consulta de base de dados** que deseja executar.
7. Clique em **introduza os valores** sob **nome de utilizador e palavra-passe** e introduza as credenciais da base de dados. Pode usar a autenticação integrada do Windows ou a autenticação do SQL Server, dependendo de como o SQL Server no local está configurado.

   ![Introduza as credenciais da base de dados](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   As alterações de "valores necessários" de mensagem para "conjunto de valores" com uma marca de verificação verde. Só tem de introduzir as credenciais de uma vez, a menos que as informações de base de dados ou a palavra-passe é alterado. Azure Machine Learning Studio (clássico) usa o certificado fornecido quando você instalou o gateway para criptografar as credenciais na nuvem. Azure nunca armazena credenciais no local sem encriptação.

   ![Importar as propriedades do módulo de dados](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Clique em **executar** para executar a experimentação.

Depois de termina a execução da experimentação, pode visualizar os dados importados da base de dados ao clicar na porta de saída do **importar dados** módulo e selecionando **Visualize**.

Depois de concluir a desenvolver a sua experimentação, pode implementar e operacionalizar o seu modelo. Utilizar o serviço de execução do Batch, dados do SQL Server no local base de dados é configurada na **importar dados** módulo será ler e utilizado para a classificação. Embora seja possível usar o serviço de resposta do pedido para a classificação de dados no local, a Microsoft recomenda utilizar o [Excel Add-in](excel-add-in-for-web-services.md) em vez disso. Atualmente, escrevendo um banco de dados do SQL Server no local através de **exportar dados** není serviços da web suportado em suas experimentações ou publicado.
