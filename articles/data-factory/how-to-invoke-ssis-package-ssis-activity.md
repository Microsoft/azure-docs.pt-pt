---
title: Execute um pacote SSIS com a atividade do Pacote SSIS executar
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) num oleoduto Azure Data Factory utilizando a atividade do Pacote SSIS Execute.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: de9cd7e77e558c4d1a0aa62af17bc612eee5ec56
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555846"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Run an SSIS package with the Execute SSIS Package activity in Azure Data Factory (Executar um pacote do SSIS com a atividade Executar Pacote do SSIS no Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) num oleoduto Azure Data Factory utilizando a atividade do Pacote SSIS Execute. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Crie um tempo de integração Azure-SSIS (IR) se ainda não tiver um seguindo as instruções passo a passo no [Tutorial: Provisioning Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal Azure
Nesta secção, utiliza a interface de utilizador da Data Factory (UI) ou a app para criar um pipeline data Factory com uma atividade do Pacote SSIS executou o seu pacote SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um oleoduto com uma atividade de Pacote SSIS executar
Neste passo, você usa a UI da Data Factory ou app para criar um pipeline. Adicione uma atividade do Pacote SSIS execute ao pipeline e configuure-o para executar o seu pacote SSIS. 

1. Na sua visão geral da Data Factory ou na página inicial no portal Azure, selecione o **azulejo do Monitor de autor &** para iniciar a UI da Data Factory ou a aplicação num separador. 

   ![Página inicial da Fábrica de Dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na página **Vamos começar**, selecione **Criar pipeline**. 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Na caixa de **ferramentas Atividades,** expandir **a General.** Em seguida, arraste uma atividade **do Pacote SSIS executar** para a superfície do designer do gasoduto. 

   ![Arraste uma atividade de pacote SSIS executada para a superfície do designer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Selecione o objeto de atividade do Pacote Executar SSIS para configurar os seus separadores **Gerais**, **Definições,** **Parâmetros SSIS,** **Gestores de Ligação** e **separadores de sobreposições** de propriedade.

#### <a name="general-tab"></a>Separador Geral

No separador **Geral** da atividade do Pacote SSIS, complete os seguintes passos.

![Definir propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Para **nome,** insira o nome da sua atividade do Pacote SSIS Executar.

   1. Para **descrição,** insira a descrição da sua atividade do Pacote SSIS Executar.

   1. Para **o timeout,** insira o tempo máximo que a sua atividade do Pacote SSIS executar pode ser executada. O padrão é de 7 dias, o formato é D.HH:MM:SS.

   1. Para **refazer,** introduza o número máximo de tentativas de repetição para a sua atividade do Pacote SSIS Executar.

   1. Para **o intervalo Desatry**, insira o número de segundos entre cada tentativa de repetição para a sua atividade 'Executar O Pacote SSIS'. O padrão é de 30 segundos.

   1. Selecione a caixa de **verificação de saída Secure** para escolher se pretende excluir a saída da sua atividade do Pacote SSIS Executar de registo.

   1. Selecione a caixa de **verificação** de entrada Secure para escolher se deseja excluir a entrada da sua atividade 'Executar O Pacote SSIS' de registar registos.

#### <a name="settings-tab"></a>Separador de definições

No **separador Definições** da atividade do Pacote SSIS Execute, complete os seguintes passos.

![Definir propriedades no separador Definições - Automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. Para **a Azure-SSIS IR,** selecione o Azure-SSIS IR designado para executar a sua atividade de Pacote SSIS executar.

   1. Para **descrição,** insira a descrição da sua atividade do Pacote SSIS Executar.

   1. Selecione a caixa de **verificação de autenticação do Windows** para escolher se pretende utilizar a autenticação do Windows para aceder a lojas de dados, tais como servidores SQL/partilhas de ficheiros no local ou Ficheiros Azure.
   
      Se selecionar esta caixa de verificação, introduza os valores das suas credenciais de execução de pacotes nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, para aceder aos Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` .

      Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Selecione a caixa **de verificação de tempo de execução de 32 Bits** para escolher se o seu pacote precisa de 32 bits de tempo de execução para ser executado.

   1. Para **localização do pacote**, selecione **SSISDB,** **Sistema de Ficheiros (Pacote)**, **Sistema de Ficheiros (Projeto)**, **Pacote Incorporado** ou Loja **de Pacotes**. 

##### <a name="package-location-ssisdb"></a>Localização do pacote: SSISDB

**SSISDB** como a sua localização do pacote é automaticamente selecionada se o seu Azure-SSIS IR forjado com um catálogo SSIS (SSISDB) hospedado pelo servidor de base de dados Azure SQL/Instância Gerida ou se você mesmo puder selecioná-lo. Se for selecionado, complete os seguintes passos.

   1. Se o seu Azure-SSIS IR estiver em execução e a caixa de verificação de **entradas manuais** estiver limpa, navegue e selecione as pastas, projetos, pacotes e ambientes existentes do SSISDB. Selecione **Refresh** para obter as suas pastas, projetos, pacotes ou ambientes recém-adicionados do SSISDB, para que estejam disponíveis para navegação e seleção. Para navegar e selecionar os ambientes para as suas execuções organizadas, tem de configurar previamente os seus projetos para adicionar esses ambientes como referências das mesmas pastas no SSISDB. Para obter mais informações, consulte [Criar e mapear ambientes SSIS.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)

   1. Para **o nível de registo de registo,** selecione um âmbito predefinido de registo para a execução do seu pacote. Selecione a caixa **de verificação personalizada** se quiser introduzir o seu nome de registo personalizado. 

   1. Se o seu Azure-SSIS IR não estiver em funcionamento ou se a caixa de verificação de **entradas manuais** for selecionada, insira os seus percursos de pacote e ambiente a partir do SSISDB diretamente nos seguintes formatos: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>` .

      ![Definir propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Localização do pacote: Sistema de Ficheiros (Pacote)

**Sistema de Ficheiros (Pacote)** como a sua localização do pacote é automaticamente selecionado se o seu Azure-SSIS IR foi a provisionado sem SSISDB ou se você pode selecioná-lo por si mesmo. Se for selecionado, complete os seguintes passos.

![Definir propriedades no separador Definições - Sistema de Ficheiros (Pacote)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Especifique o seu pacote para executar fornecendo um caminho da Convenção Universal de Nomeação (UNC) para o seu ficheiro de pacote (com `.dtsx` ) na caixa de caminho **pacote.** Pode navegar e selecionar o seu pacote selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar o seu pacote em Ficheiros Azure, o seu caminho é `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Se configurar o seu pacote num ficheiro separado, também precisa de fornecer um caminho UNC para o seu ficheiro de configuração `.dtsConfig` (com) na caixa de caminho de **configuração.** Pode navegar e selecionar a sua configuração selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar a sua configuração em Ficheiros Azure, o seu caminho é `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Especifique as credenciais para aceder aos seus ficheiros de embalagem e configuração. Se tiver introduzido previamente os valores das suas credenciais de execução do pacote (para **autenticação do Windows),** pode reutilizá-los selecionando a **caixa de verificação das credenciais** de execução de pacotes. Caso contrário, introduza os valores das credenciais de acesso ao seu pacote nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar o seu pacote e configuração em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . 

      Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

      Estas credenciais também são usadas para aceder aos pacotes do seu filho na Tarefa do Pacote Executar que são referenciados pelo seu próprio caminho e outras configurações especificadas nas suas embalagens. 

   1. Se utilizou o nível de proteção **EncriptarAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou o seu pacote através de SQL Server Data Tools (SSDT), insira o valor da sua palavra-passe na caixa **de palavras-passe de Encriptação.** Em alternativa, pode utilizar um segredo armazenado no seu Cofre de Chaves Azure como seu valor (ver acima).
      
      Se utilizar o nível de proteção **CrisensitiveWithUserKey,** reintesse os seus valores sensíveis em ficheiros de configuração ou nos **parâmetros SSIS,** **Gestores de Ligação** ou **separadores de propriedade** (ver abaixo).
      
      Se usou o nível de proteção **EncriptaAllWithUserKey,** não é suportado. É necessário reconfigurar a sua embalagem para utilizar outro nível de proteção através do SSDT ou do `dtutil` utilitário da linha de comando. 

   1. Para **o nível de registo de registo,** selecione um âmbito predefinido de registo para a execução do seu pacote. Selecione a caixa **de verificação personalizada** se quiser introduzir o seu nome de registo personalizado. 
   
   1. Se pretender registar as execuções do seu pacote para além da utilização dos fornecedores padrão de registo que podem ser especificados no seu pacote, especifique a sua pasta de registo fornecendo o seu caminho UNC na caixa de **caminho de registo.** Pode navegar e selecionar a sua pasta de registo selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo é `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma sub-dobradeira é criada neste caminho para cada pacote executado, nomeado após o Executo SSIS Package executar ID, e no qual os ficheiros de registo são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para aceder à sua pasta de registo. Se já introduziu os valores das suas credenciais de acesso ao pacote (ver acima), pode reutilizá-las selecionando a caixa de verificação **de credenciais** de acesso a pacotes. Caso contrário, introduza os valores das suas credenciais de acesso ao registo nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar os seus registos em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores (ver acima).
   
Para todos os caminhos da UNC mencionados anteriormente, o nome de ficheiro totalmente qualificado deve ser inferior a 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-file-system-project"></a>Localização do pacote: Sistema de Ficheiros (Projeto)

Se selecionar **o Sistema de Ficheiros (Projeto)** como localização do seu pacote, complete os seguintes passos.

![Definir propriedades no separador Definições - Sistema de Ficheiros (Projeto)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Especifique o seu pacote para executar fornecendo um caminho UNC para o seu arquivo de projeto `.ispac` (com) na caixa de caminho do **Projeto** e um ficheiro de pacote (com ) do seu projeto na caixa de `.dtsx` nome **pacote.** Pode navegar e selecionar o seu projeto selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar o seu projeto em Ficheiros Azure, o seu caminho é `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Especifique as credenciais para aceder ao seu projeto e ficheiros de pacotes. Se tiver introduzido previamente os valores das suas credenciais de execução do pacote (para **autenticação do Windows),** pode reutilizá-los selecionando a **caixa de verificação das credenciais** de execução de pacotes. Caso contrário, introduza os valores das credenciais de acesso ao seu pacote nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar o seu projeto e pacote em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . 

      Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

      Estas credenciais também são usadas para aceder aos pacotes do seu filho na Tarefa do Pacote Executar que são referenciados a partir do mesmo projeto. 

   1. Se usou o nível de proteção **EncriptarAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou o seu pacote via SSDT, insira o valor da sua palavra-passe na caixa de senha de **encriptação.** Em alternativa, pode utilizar um segredo armazenado no seu Cofre de Chaves Azure como seu valor (ver acima).
      
      Se utilizar o nível de proteção **CriensitiveWithUserKey,** reintesse os seus valores sensíveis nos **parâmetros SSIS,** **Gestores de Ligação** ou **separadores de propriedade** (ver abaixo).
      
      Se usou o nível de proteção **EncriptaAllWithUserKey,** não é suportado. É necessário reconfigurar a sua embalagem para utilizar outro nível de proteção através do SSDT ou do `dtutil` utilitário da linha de comando. 

   1. Para **o nível de registo de registo,** selecione um âmbito predefinido de registo para a execução do seu pacote. Selecione a caixa **de verificação personalizada** se quiser introduzir o seu nome de registo personalizado. 
   
   1. Se pretender registar as execuções do seu pacote para além da utilização dos fornecedores padrão de registo que podem ser especificados no seu pacote, especifique a sua pasta de registo fornecendo o seu caminho UNC na caixa de **caminho de registo.** Pode navegar e selecionar a sua pasta de registo selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo é `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma sub-dobradeira é criada neste caminho para cada pacote executado, nomeado após o Executo SSIS Package executar ID, e no qual os ficheiros de registo são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para aceder à sua pasta de registo. Se já introduziu os valores das suas credenciais de acesso ao pacote (ver acima), pode reutilizá-las selecionando a caixa de verificação **de credenciais** de acesso a pacotes. Caso contrário, introduza os valores das suas credenciais de acesso ao registo nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar os seus registos em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores (ver acima).
   
Para todos os caminhos da UNC mencionados anteriormente, o nome de ficheiro totalmente qualificado deve ser inferior a 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-embedded-package"></a>Localização do pacote: Pacote incorporado

Se selecionar **o pacote Incorporado** como localização do seu pacote, complete os seguintes passos.

![Definir propriedades no separador Definições - Pacote incorporado](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Arraste e deixe cair o seu ficheiro de pacote `.dtsx` (com) ou **carregue-o** a partir de uma pasta de ficheiros na caixa fornecida. O seu pacote será automaticamente comprimido e incorporado na carga útil da atividade. Uma vez incorporado, pode **descarregar** o seu pacote mais tarde para edição. Também pode **parametrizar** o seu pacote incorporado atribuindo-o a um parâmetro de pipeline que pode ser usado em múltiplas atividades, otimizando assim o tamanho da sua carga útil do pipeline. A incorporação de ficheiros de projeto `.ispac` (com) não é suportada atualmente, pelo que não pode utilizar parâmetros/gestores de conexão SSIS com âmbito de nível de projeto nos seus pacotes incorporados.
   
   1. Se o seu pacote incorporado não estiver todo encriptado e detetarmos a utilização da Tarefa do Pacote Executo (EPT) nele, a caixa de verificação **de tarefas do pacote executar** será automaticamente selecionada e as suas embalagens para crianças referenciadas pelo seu caminho do sistema de ficheiros serão automaticamente adicionadas, para que também as possa incorporar.
   
      Se não conseguirmos detetar a utilização do EPT, tem de selecionar manualmente a caixa de verificação **de tarefas do pacote executar** e adicionar as suas embalagens para crianças referenciadas pelo seu caminho do sistema de ficheiros um a um, para que também possa inseri-las. Se as suas embalagens para crianças estiverem armazenadas na base de dados do SQL Server (MSDB), não pode inseri-las, pelo que tem de garantir que o seu Azure-SSIS IR pode aceder ao MSDB para os ir buscar utilizando as suas referências SQL Server. A incorporação de ficheiros de projetos `.ispac` (com) não é suportada atualmente, pelo que não pode utilizar referências baseadas em projetos para os seus pacotes infantis.
   
   1. Se usou o nível de proteção **EncriptarAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou o seu pacote via SSDT, insira o valor da sua palavra-passe na caixa de senha de **encriptação.** 
   
      Em alternativa, pode utilizar um segredo armazenado no seu Cofre de Chaves Azure como seu valor. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Se utilizar o nível de proteção **CrisensitiveWithUserKey,** reintesse os seus valores sensíveis em ficheiros de configuração ou nos **parâmetros SSIS,** **Gestores de Ligação** ou **separadores de propriedade** (ver abaixo).
      
      Se usou o nível de proteção **EncriptaAllWithUserKey,** não é suportado. É necessário reconfigurar a sua embalagem para utilizar outro nível de proteção através do SSDT ou do `dtutil` utilitário da linha de comando.

   1. Para **o nível de registo de registo,** selecione um âmbito predefinido de registo para a execução do seu pacote. Selecione a caixa **de verificação personalizada** se quiser introduzir o seu nome de registo personalizado. 
   
   1. Se pretender registar as execuções do seu pacote para além da utilização dos fornecedores padrão de registo que podem ser especificados no seu pacote, especifique a sua pasta de registo fornecendo o seu caminho UNC na caixa de **caminho de registo.** Pode navegar e selecionar a sua pasta de registo selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo é `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma sub-dobradeira é criada neste caminho para cada pacote executado, nomeado após o Executo SSIS Package executar ID, e no qual os ficheiros de registo são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para aceder à sua pasta de registo introduzindo os seus valores nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar os seus registos em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores (ver acima).
   
Para todos os caminhos da UNC mencionados anteriormente, o nome de ficheiro totalmente qualificado deve ser inferior a 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

##### <a name="package-location-package-store"></a>Localização do pacote: Loja de pacotes

Se selecionar **a loja Pacote** como localização da sua embalagem, complete os seguintes passos.

![Definir propriedades no separador Definições - Loja de pacotes](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. Para **o nome da loja Pacote**, selecione uma loja de pacotes existente que está anexada ao seu Azure-SSIS IR.

   1. Especifique a sua embalagem para executar fornecendo o seu caminho (sem `.dtsx` ) a partir da loja de pacotes selecionada na caixa de caminho **pacote.** Se a loja de pacotes selecionada estiver em cima do sistema de ficheiros/Ficheiros Azure, pode navegar e selecionar o seu pacote selecionando **o armazenamento de ficheiros Browse,** caso contrário poderá introduzir o seu caminho no formato de `<folder name>\<package name>` . Também pode importar novos pacotes para a loja de pacotes selecionada através do SQL Server Management Studio (SSMS) semelhante à [antiga loja de pacotes SSIS](/sql/integration-services/service/package-management-ssis-service). Para obter mais informações, consulte [os pacotes SSIS com lojas de pacotes Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).

   1. Se configurar o seu pacote num ficheiro separado, tem de fornecer um caminho UNC para o seu ficheiro de configuração `.dtsConfig` (com) na caixa de caminho de **configuração.** Pode navegar e selecionar a sua configuração selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar a sua configuração em Ficheiros Azure, o seu caminho é `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Selecione a caixa de **verificação de credenciais** de acesso de Configuração para escolher se deseja especificar as credenciais para aceder separadamente ao seu ficheiro de configuração. Isto é necessário quando a loja de pacotes selecionada está no topo da base de dados do SQL Server (MSDB) hospedada pela sua Azure SQL Managed Instance ou não armazena também o seu ficheiro de configuração.
   
      Se tiver introduzido previamente os valores das suas credenciais de execução do pacote (para **autenticação do Windows),** pode reutilizá-los selecionando a **caixa de verificação das credenciais** de execução de pacotes. Caso contrário, introduza os valores para as suas credenciais de acesso à configuração nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar a sua configuração em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . 

      Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Se usou o nível de proteção **EncriptarAllWithPassword** ou **EncryptSensitiveWithPassword** quando criou o seu pacote via SSDT, insira o valor da sua palavra-passe na caixa de senha de **encriptação.** Em alternativa, pode utilizar um segredo armazenado no seu Cofre de Chaves Azure como seu valor (ver acima).
      
      Se utilizar o nível de proteção **CrisensitiveWithUserKey,** reintesse os seus valores sensíveis em ficheiros de configuração ou nos **parâmetros SSIS,** **Gestores de Ligação** ou **separadores de propriedade** (ver abaixo).
      
      Se usou o nível de proteção **EncriptaAllWithUserKey,** não é suportado. É necessário reconfigurar a sua embalagem para utilizar outro nível de proteção através do SSDT ou do `dtutil` utilitário da linha de comando. 

   1. Para **o nível de registo de registo,** selecione um âmbito predefinido de registo para a execução do seu pacote. Selecione a caixa **de verificação personalizada** se quiser introduzir o seu nome de registo personalizado. 
   
   1. Se pretender registar as execuções do seu pacote para além da utilização dos fornecedores padrão de registo que podem ser especificados no seu pacote, especifique a sua pasta de registo fornecendo o seu caminho UNC na caixa de **caminho de registo.** Pode navegar e selecionar a sua pasta de registo selecionando **o armazenamento de ficheiros Browse ou insira** o seu caminho manualmente. Por exemplo, se armazenar os seus registos em Ficheiros Azure, o seu caminho de registo é `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Uma sub-dobradeira é criada neste caminho para cada pacote executado, nomeado após o Executo SSIS Package executar ID, e no qual os ficheiros de registo são gerados a cada cinco minutos. 
   
   1. Especifique as credenciais para aceder à sua pasta de registo introduzindo os seus valores nas caixas **De Domínio,** **Nome de Utilizador** e **Password.** Por exemplo, se armazenar os seus registos em Ficheiros Azure, o domínio é `Azure` , o nome de utilizador é , e a `<storage account name>` palavra-passe é `<storage account key>` . Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores (ver acima).
   
Para todos os caminhos da UNC mencionados anteriormente, o nome de ficheiro totalmente qualificado deve ser inferior a 260 caracteres. O nome do diretório deve ter menos de 248 caracteres.

#### <a name="ssis-parameters-tab"></a>Separador de parâmetros SSIS

No separador **parâmetros SSIS** da atividade do Pacote SSIS, complete os seguintes passos.

![Definir propriedades no separador parâmetros SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Se o seu Azure-SSIS IR estiver em execução, **o SSISDB** é selecionado como a **sua localização** do pacote, e a caixa de verificação de entradas manual no separador **Definições** é apurada, os parâmetros SSIS existentes no seu projeto selecionado e pacote do SSISDB são apresentados para que você atribua valores aos mesmos. Caso contrário, pode inscrevê-los um a um para lhes atribuir valores manualmente. Certifique-se de que existem e que estão corretamente inseridos para que a sua execução do pacote tenha sucesso. 
   
   1. Se utilizou o nível de proteção **CrisensitiveWithUserKey** quando criou o seu pacote através de SSDT e **Sistema de Ficheiros (Pacote)**, **Sistema de Ficheiros (Projeto)**, **Pacote Incorporado** ou Loja **de Pacotes** é selecionado como localização do seu pacote, também precisa de reentrar nos seus parâmetros sensíveis para lhes atribuir valores neste separador. 
   
Quando atribui valores aos seus parâmetros, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory.

Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Separador de Gestores de Ligação

No **separador Gestores** de Ligação da atividade do Pacote SSIS, complete os seguintes passos.

![Definir propriedades no separador Gestores de Ligação](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Se o seu Azure-SSIS IR estiver em execução, **o SSISDB** é selecionado como a **sua localização** do pacote, e a caixa de verificação de entradas manual no separador Definições é apurada, os gestores de ligação **existentes** no seu projeto selecionado e pacote do SSISDB são apresentados para que você atribua valores às suas propriedades. Caso contrário, pode inscrevê-los um a um para atribuir valores às suas propriedades manualmente. Certifique-se de que existem e que estão corretamente inseridos para que a sua execução do pacote tenha sucesso. 
   
      Pode obter os nomes **corretos do SCOPE,** **NAME** e **PROPERTY** para qualquer gestor de ligação, abrindo o pacote que o contém no SSDT. Após a abertura do pacote, selecione o gestor de ligação relevante para mostrar os nomes e valores de todas as suas propriedades na janela **Propriedades** de SSDT. Com esta informação, pode anular os valores de quaisquer propriedades do gestor de ligação no tempo de execução. 

      ![Obtenha propriedades do gestor de conexão a partir de SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Por exemplo, sem modificar o seu pacote original em SSDT, pode converter os seus fluxos de dados no local para as instalações que funcionam no SQL Server em fluxos de dados em nuvem em funcionamento no SSIS IR em ADF, sobressaltando os valores das propriedades da **ConnectByProxy,** **ConnectionString** e **ConnectUsingManagedIdentity** em gestores de conexão existentes no tempo de execução.
      
      Estas sobreposições de tempo de execução podem permitir Self-Hosted IR (SHIR) como representante do SSIS IR ao aceder aos dados nas instalações, ver [Configurar o SHIR como um proxy para as](./self-hosted-integration-runtime-proxy-ssis.md)ligações SSIS IR , e Azure SQL Database/Managed Instance utilizando o mais recente controlador MSOLEDBSQL que, por sua vez, permite a autenticação do Azure Ative Directory (AAD) com identidade gerida pela ADF, ver [autenticação AAD configurada com identidade gerida pela ADF para ligações OLEDB](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication).

      ![Definir propriedades a partir de SSDT no separador Gestores de Ligação](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Se utilizou o nível de proteção **CriptografiaSensitiveWithUserKey** quando criou o seu pacote através de SSDT e **Sistema de Ficheiros (Pacote)**, **Sistema de Ficheiros (Projeto)**, **Pacote Incorporado** ou Loja **de Pacotes** é selecionado como localização do seu pacote, também precisa de reentrar nas propriedades do seu gestor de ligação sensível para atribuir valores a eles neste separador. 

Quando atribui valores às propriedades do seu gestor de ligação, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory. 

Em alternativa, pode utilizar os segredos armazenados no seu Cofre de Chaves Azure como valores. Para tal, selecione a caixa de verificação **AZURE KEY VAULT** ao lado. Selecione ou edite o serviço de ligação ao cofre de chaves existente ou crie um novo. Em seguida, selecione o nome secreto e a versão para o seu valor. Quando criar ou editar o serviço ligado ao cofre de chaves, pode selecionar ou editar o cofre de chaves existente ou criar um novo. Certifique-se de conceder à Data Factory acesso de identidade gerido ao seu cofre de chaves se ainda não o fez. Também pode inserir o seu segredo diretamente no seguinte formato: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Separador de sobreposições de propriedade

No **separador Propriedade Sobrepõe-se** à atividade do Pacote SSIS Execute, complete os seguintes passos.

![Definir propriedades no separador Propriedades Sobrepõe-se](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Introduza os caminhos das propriedades existentes no seu pacote selecionado, um a um, para lhes atribuir valores manualmente. Certifique-se de que existem e que estão corretamente inseridos para que a sua execução do pacote tenha sucesso. Por exemplo, para anular o valor da variável do utilizador, insira o seu caminho no seguinte formato: `\Package.Variables[User::<variable name>].Value` . 

      Pode obter o **CAMINHO DE PROPRIEDADE** correto para qualquer propriedade de pacote, abrindo o pacote que o contém em SSDT. Após a abertura do pacote, selecione o seu fluxo de controlo e a propriedade **Configurações** na janela **Propriedades** do SSDT. Em seguida, selecione o botão **elipse** **(...**) ao lado da sua propriedade Configurações para abrir o **Organizador de Configurações** de Pacotes que é normalmente utilizado para criar configurações de pacote no Modelo de [Implementação de Pacotes](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Obtenha propriedades de pacotes a partir de propriedade SSDT - Configurações](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      No **Organizador de Configurações de Pacotes**, selecione a caixa de **verificação de configurações de pacotes de ativação** e o botão **Adicionar...** para abrir o **Assistente de Configuração de Pacotes**. 
      
      No Assistente de Configuração de **Pacotes**, selecione o item **de ficheiro de configuração XML** no menu de retirada **do tipo de configuração** de configuração e as **definições de configuração de especificar diretamente,** introduzir o nome do ficheiro de configuração e selecionar o botão **seguinte >.** 

      ![Obtenha propriedades de pacotes da SSDT - Organizador de Configurações](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Por fim, selecione as propriedades do pacote cujo caminho deseja e o botão **Next >.**  Agora pode ver, copiar & colar os caminhos da propriedade do pacote que deseja e guardá-los no seu ficheiro de configuração. Com esta informação, pode sobrepor-se aos valores de quaisquer propriedades do pacote no tempo de execução. 

      ![Obtenha propriedades de pacotes de SSDT - Assistente de configuração](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Se utilizou o nível de proteção **CrisensitiveWithUserKey** quando criou o seu pacote através de SSDT e **Sistema de Ficheiros (Pacote)**, **Sistema de Ficheiros (Projeto)**, **Pacote Incorporado** ou Loja **de Pacotes** é selecionado como localização do seu pacote, também precisa de reentrar nas suas propriedades de pacotes sensíveis para lhes atribuir valores neste separador. 
   
Quando atribui valores às propriedades do seu pacote, pode adicionar conteúdo dinâmico utilizando expressões, funções, variáveis do sistema data factory e parâmetros ou variáveis do pipeline data Factory.

Os valores atribuídos em ficheiros de configuração e no separador **Parâmetros SSIS** podem ser ultrapassados utilizando os **separadores De Gestores de Ligação** ou **Substituições de Propriedade.** Os valores atribuídos no **separador Connection Managers** também podem ser ultrapassados utilizando o **separador Propriedade Overrides.**

Para validar a configuração do pipeline, **selecione Validate** na barra de ferramentas. Para encerrar o **Relatório de Validação** do Gasoduto, selecione **>>** .

Para publicar o pipeline na Data Factory, **selecione Publicar Tudo**. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Neste passo, desencadeia-se uma corrida de oleoduto. 

1. Para ativar uma execução do gasoduto, selecione **Trigger** na barra de ferramentas e selecione **Trigger agora**. 

   ![Acionar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Vê o gasoduto a funcionar e o seu estado juntamente com outras informações, como a hora **de início de funcionação.** Para atualizar a vista, selecione **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecione a ligação **Ver Execuções de Atividades** na coluna **Ações**. Só se vê uma atividade a funcionar porque o oleoduto tem apenas uma atividade. É a atividade do Pacote SSIS executar.

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Execute a seguinte consulta com a base de dados SSISDB no seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Verificar execuções de pacotes](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Também pode obter o ID de execução SSISDB a partir da saída da atividade do pipeline e usar o ID para verificar registos de execução mais abrangentes e mensagens de erro no SQL Server Management Studio.

   ![Obter a identificação da execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agende o oleoduto com um gatilho

Também pode criar um gatilho programado para o seu oleoduto para que o gasoduto seja executado num horário, como de hora a hora ou diariamente. Por exemplo, consulte [Criar uma fábrica de dados - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com PowerShell
Nesta secção, utiliza a Azure PowerShell para criar um pipeline data Factory com uma atividade do Pacote SSIS executou o seu pacote SSIS. 

Instale os mais recentes módulos Azure PowerShell seguindo as instruções passo a passo em [Como instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Criar uma fábrica de dados com a Azure-SSIS IR
Pode utilizar uma fábrica de dados existente que já tenha a Azure-SSIS IR a provisionada ou criar uma nova fábrica de dados com a Azure-SSIS IR. Siga as instruções passo a passo no [Tutorial: Implemente os pacotes SSIS para Azure via PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um oleoduto com uma atividade de Pacote SSIS executar 
Neste passo, cria-se um oleoduto com uma atividade do Pacote SSIS Executar. A atividade executa o seu pacote SSIS. 

1. Crie um ficheiro JSON nomeado `RunSSISPackagePipeline.json` na pasta com conteúdo semelhante ao seguinte `C:\ADF\RunSSISPackage` exemplo.

   > [!IMPORTANT]
   > Substitua nomes de objetos, descrições e caminhos, valores de propriedade ou parâmetros, palavras-passe e outros valores variáveis antes de guardar o ficheiro. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Para executar pacotes armazenados no sistema de ficheiros/Ficheiros Azure, insira os valores para as propriedades da sua embalagem e registo da localização da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes dentro de projetos armazenados no sistema de ficheiros/Ficheiros Azure, insira os valores das propriedades de localização do seu pacote da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes incorporados, insira os valores das propriedades de localização do seu pacote da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Para executar pacotes armazenados em lojas de pacotes, insira os valores para as propriedades de localização do seu pacote e configuração da seguinte forma:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Em Azure PowerShell, mude para a `C:\ADF\RunSSISPackage` pasta.

3. Para criar o gasoduto **RunSSISPackagePipeline,** executar o **cmdlet Set-AzDataFactoryV2Pipeline.**

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Aqui está a saída da amostra:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Utilize o **cmdlet Invoke-AzDataFactoryV2Pipeline** para executar o gasoduto. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie ou cole o seguinte script na janela PowerShell e selecione Enter. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Também pode monitorizar o gasoduto utilizando o portal Azure. Para obter instruções passo a passo, consulte [o gasoduto](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Agende o oleoduto com um gatilho
No passo anterior, fez o oleoduto a pedido. Também pode criar um gatilho de horário para executar o pipeline em um horário, como de hora em hora ou diariamente.

1. Criar um ficheiro JSON nomeado `MyTrigger.json` na pasta com o seguinte `C:\ADF\RunSSISPackage` conteúdo: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Em Azure PowerShell, mude para a `C:\ADF\RunSSISPackage` pasta.
1. Executar o **cmdlet Set-AzDataFactoryV2Trigger,** que cria o gatilho. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Por defeito, o gatilho está em estado de paragem. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger.** 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger.** 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Executar o seguinte comando depois da hora seguinte. Por exemplo, se a hora atual for 3:25 PM UTC, executar o comando às 16:00 UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Execute a seguinte consulta com a base de dados SSISDB no seu servidor SQL para verificar se o pacote foi executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte publicação de blog:
- [Modernizar e alargar os seus fluxos de trabalho ETL/ELT com atividades SSIS em oleodutos Azure Data Factory](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)