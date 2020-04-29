---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar O Azure FarmBeats na sua subscrição azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479564"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar o Azure FarmBeats na sua subscrição azure.

A Azure FarmBeats é uma oferta de negócios a negócios disponível no Azure Marketplace. Permite a agregação de conjuntos de dados agrícolas entre fornecedores e geração de insights atuais. O Azure FarmBeats fá-lo permitindo-lhe construir modelos de inteligência artificial (IA) ou machine learning (ML) com base em conjuntos de dados fundidos. Os dois principais componentes da Azure FarmBeats são:

- **Datahub**: Uma camada DePi que permite agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre diferentes fornecedores.

- **Acelerador**: Aplicação web que é construída em cima do Datahub. Inicia o desenvolvimento e visualização do seu modelo. O acelerador utiliza APIs Azure FarmBeats para demonstrar a visualização de dados de sensores ingeridos como gráficos e visualização da saída do modelo como mapas.

## <a name="general-information"></a>Informações gerais

### <a name="components-installed"></a>Componentes instalados

Quando instala o Azure FarmBeats, os seguintes recursos são aprovisionados na sua subscrição Azure:

| Recursos Azure Instalados  | Componente Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Acelerador de & datahub      |
| Serviço de Aplicações     |     Acelerador de & datahub     |
| Plano do Serviço de Aplicações   | Acelerador de & datahub  |
| Conexão API    |  Datahub       |
| Cache do Azure para Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acelerador de & datahub      |
| Conta do Azure Batch    | Datahub   |
| Azure Key Vault |  Acelerador de & datahub        |
| Conta Azure Maps       |     Acelerador    |
| Espaço de nome do hub de eventos    |     Datahub      |
| Aplicação Lógica      |  Datahub       |
| Conta de Armazenamento      |     Acelerador de & datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo do Azure FarmBeats é um agregado do custo dos serviços Azure subjacentes. As informações sobre preços dos serviços Azure podem ser calculadas através da [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total variará em função da utilização. O custo estatal estável para os dois componentes é:

- Datahub - menos de $10 por dia
- Acelerador - menos de $2 por dia

### <a name="regions-supported"></a>Regiões apoiadas

Atualmente, o Azure FarmBeats é suportado em ambientes públicos de nuvem nas seguintes regiões:

- Leste da Austrália
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Oeste
- E.U.A.Oeste 2
- Europa do Norte
- Europa ocidental
- Ásia Leste
- Sudeste Asiático

### <a name="time-taken"></a>Tempo tomado

Toda a configuração de Azure FarmBeats, incluindo a preparação e instalação, levará menos de uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Terá de completar os seguintes passos antes de iniciar a instalação real de Azure FarmBeats:

### <a name="verify-permissions"></a>Verificar Permissões

Você precisará das seguintes permissões no inquilino Azure para instalar Azure FarmBeats:

- Inquilino - Criador de aplicações AAD
- Assinatura - Proprietário
- Grupo de Recursos no qual farmBeats está sendo instalado - Proprietário

As duas primeiras permissões são necessárias para criar a etapa de [aplicação da AAD.](#create-an-aad-application) Se necessário, pode obter alguém com as permissões adequadas para criar a aplicação AAD.

A pessoa que gere a instalação FarmBeats do mercado tem de ser proprietária do Grupo de Recursos no qual o FarmBeats está a ser instalado. Para os proprietários de subscrições, isto acontece automaticamente quando o Grupo de Recursos é criado. Para outros, por favor, crie o Grupo de Recursos e peça ao proprietário da Subscrição que o torne proprietário do Grupo de Recursos.

Pode verificar as suas permissões de acesso no portal Azure seguindo as instruções sobre o controlo de acesso baseado em [funções](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Decidir Subscrição e Região

Você precisará do ID de subscrição Azure e da região onde você deseja instalar Azure FarmBeats. Escolha uma das regiões listadas na secção apoiada pelas [Regiões.](#regions-supported)

Tome nota do ID de **subscrição azure** e da **região de Azure.**

### <a name="create-an-aad-application"></a>Criar uma aplicação AAD

A Azure FarmBeats requer criação e registo de candidaturas de Diretório Ativo Azure. Para executar com sucesso o script de criação aAD, são necessárias as seguintes permissões:

- Inquilino - Criador de aplicações AAD
- Assinatura - Proprietário

Execute os seguintes passos numa instância Cloud Shell utilizando o ambiente PowerShell. Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Complete a configuração conforme instruído.

1. Descarregue o script do gerador de [aplicativos AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Por predefinição, o ficheiro é descarregado para o seu diretório inicial. Navegue para o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. O script pede as seguintes três inputs:

    - **FarmBeats Nome do site**: Este é o prefixo único de URL para a sua aplicação web FarmBeats. No caso de o prefixo já estar tomado, o script irá errar. Uma vez instalado, a sua implementação\<FarmBeats estará acessível a partir de https:// farmBeats-website-name\<>.azurewebsites.net e as APIs swagger estarão em https:// farmBeats-website-nome>-api.azurewebsites.net

    - ID de **login Azure**: Forneça id de login Azure para o utilizador que pretende ser adicionado como administrador do FarmBeats. Este utilizador pode então conceder acesso à aplicação web FarmBeats a outros utilizadores. O ID de login é john.doe@domain.comgeralmente do formulário . A Azure UPN também é apoiada.

    - **ID de subscrição**: Este é o ID da subscrição em que pretende instalar O Azure FarmBeats

5. O script AAD demora cerca de 2 minutos a executar e a saída saem valores no ecrã, bem como a um ficheiro json no mesmo diretório. Se tivesses mais alguém a executar o guião, pede-lhes que partilhem esta produção contigo.

### <a name="create-sentinel-account"></a>Criar conta Sentinel

A sua configuração Azure FarmBeats permite-lhe obter imagens de satélite da missão satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência Espacial Europeia para a sua quinta. Para configurar esta configuração, é necessário uma conta Sentinel.

Siga os passos para criar uma conta gratuita com o Sentinel:

1. Vá à página [oficial de inscrição.](https://aka.ms/SentinelRegistration)
2. Forneça os detalhes necessários (primeiro nome, apelido, nome de utilizador, palavra-passe e ID de e-mail) e preencha o formulário.
3. Um link de verificação é enviado para o ID de e-mail registado. Selecione o link fornecido no e-mail e complete a verificação.

O seu processo de registo está completo. Tome nota do seu Nome de **Utilizador Sentinel** e **Palavra-passe Sentinela,** uma vez que a verificação também esteja concluída.

## <a name="install"></a>Instalar

Está agora pronto para instalar FarmBeats. Siga os passos abaixo para iniciar a instalação:

1. Inicie sessão no Portal do Azure. Selecione a sua conta no canto superior direito e mude para o inquilino Azure AD onde pretende instalar o Azure FarmBeats.

2. Vá ao Azure Marketplace dentro do portal e procure **o Azure FarmBeats** no Mercado.

3. Aparece uma nova janela com uma visão geral do Azure FarmBeats. Selecione **Criar**.

4. Aparece uma nova janela. Complete o processo de inscrição escolhendo a subscrição correta, grupo de recursos e localização para a qual pretende instalar o Azure FarmBeats.

5. Forneça o endereço de e-mail que deve receber quaisquer alertas de serviço relacionados com o Azure FarmBeats na secção **Dealertas de Serviço FarmBeats.** Selecione **Seguinte** na parte inferior da página para passar para o Separador **Dependências.**

    ![Separador Básico](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copie as entradas individuais da saída do [script AAD](#create-an-aad-application) para as entradas na secção de aplicação AAD.

7. Introduza o nome de utilizador e a palavra-passe da [conta Sentinel](#create-sentinel-account) na secção Conta Sentinel. Selecione **Next** para mover para o separador **Rever + Criar.**

    ![Guia de Dependências](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Assim que os detalhes introduzidos forem validados, selecione **OK**. A página Termos de utilização aparece. Reveja os termos e selecione **Criar** para iniciar a instalação. Será redirecionado para a página onde poderá acompanhar o progresso da instalação.

Uma vez concluída a instalação, pode verificar a instalação e começar a utilizar o\<portal FarmBeats navegando para o nome do site que forneceu durante a instalação: https:// FarmBeats-website-name>.azurewebsites.net. Deve ver a interface de utilizador farmBeats com uma opção para criar Quintas.

**Datahub** pode ser\<encontrado em https:// farmBeats-website-name>-api.azurewebsites.net/swagger. Aqui você verá os diferentes objetos FarmBeats API e realizará operações DE REST nas APIs.

## <a name="upgrade"></a>Atualizar

Para atualizar o FarmBeats para a versão mais recente, execute os seguintes passos numa instância cloud Shell utilizando o ambiente PowerShell. O utilizador terá de ser o proprietário da subscrição na qual o FarmBeats está instalado.

Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Complete a configuração conforme instruído.

1. Descarregue o script de [upgrade](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Por predefinição, o ficheiro é descarregado para o seu diretório inicial. Navegue para o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script de upgrade

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

O caminho para o ficheiro input.json é opcional. Se não especificado, o script pedirá todas as inputs necessárias. A atualização deve terminar em cerca de 30 minutos.

## <a name="uninstall"></a>Desinstalar

Para desinstalar o Azure FarmBeats Datahub ou Acelerador, complete os seguintes passos:

1. Inicie sessão no portal Azure e **elimine os grupos de recursos** em que estes componentes estão instalados.

2. Vá ao Azure Ative Directory & **eliminar a aplicação Azure AD** ligada à instalação Azure FarmBeats.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a instalar o Azure FarmBeats na sua subscrição Azure. Agora, aprenda a [adicionar utilizadores](manage-users-in-azure-farmbeats.md#manage-users) à sua instância Azure FarmBeats.
