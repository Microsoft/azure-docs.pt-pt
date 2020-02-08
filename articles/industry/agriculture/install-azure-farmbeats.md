---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 7d3c70695000ae62f374bc558d4b408733968b83
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069290"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure.

A Azure FarmBeats é uma oferta de negócios a negócios disponível no Azure Marketplace. Ele permite a agregação de conjuntos de dados de agricultura entre provedores e geração de informações acionáveis. O Azure FarmBeats faz isso permitindo que você crie modelos de ia (inteligência artificial) ou de ML (aprendizado de máquina) com base em conjuntos de dados com fusível. Os dois principais componentes do Azure FarmBeats são:

- **Datahub**: Uma camada DePi que permite agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre diferentes fornecedores.

- **Acelerador**: Aplicação web que é construída em cima do Datahub. Ele inicia o desenvolvimento e a visualização do modelo. O acelerador usa as APIs FarmBeats do Azure para demonstrar a visualização de dados de sensor ingeridos como gráficos e visualização de saída de modelo como mapas.

## <a name="general-information"></a>Informações gerais

### <a name="components-installed"></a>Componentes instalados

Quando você instala o Azure FarmBeats, os seguintes recursos são provisionados em sua assinatura do Azure:

| Recursos do Azure instalados  | Componente FarmBeats do Azure  |
|---------|---------|
| Application Insights   |      Acelerador de & Datahub      |
| Serviço de Aplicações     |     Acelerador de & Datahub     |
| Plano do Serviço de Aplicações   | Acelerador de & Datahub  |
| Conexão de API    |  Datahub       |
| Cache do Azure para Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acelerador de & Datahub      |
| Conta Azure Batch    | Datahub   |
| Azure Key Vault |  Acelerador de & Datahub        |
| Conta do Azure Maps       |     Accelerator    |
| Namespace do hub de eventos    |     Datahub      |
| Aplicação Lógica      |  Datahub       |
| Conta de Armazenamento      |     Acelerador de & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo da FarmBeats do Azure é uma agregação do custo dos serviços do Azure subjacentes. As informações sobre preços dos serviços Azure podem ser calculadas através da [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total irá variar com base no uso. O custo de estado estacionário para os dois componentes é:

- Datahub-menos de $10 por dia
- Acelerador-menos de $2 por dia

### <a name="regions-supported"></a>Regiões com suporte

Atualmente, o Azure FarmBeats tem suporte em ambientes de nuvem pública nas seguintes regiões:

- Leste da Austrália
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Oeste
- E.U.A.Oeste 2
- Europa do Norte
- Europa Ocidental
- Ásia Oriental
- Sudeste Asiático

### <a name="time-taken"></a>Tempo decorrido

Toda a configuração do Azure FarmBeats, incluindo a preparação e a instalação, levará menos de uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Terá de completar os seguintes passos antes de iniciar a instalação real de Azure FarmBeats:

### <a name="verify-permissions"></a>Verificar permissões

Você precisará das seguintes permissões no inquilino Azure para instalar Azure FarmBeats:

- Inquilino - Criador de aplicações AAD
- Assinatura - Proprietário
- Grupo de Recursos no qual farmBeats está sendo instalado - Proprietário

As duas primeiras permissões são necessárias para criar a etapa de [aplicação da AAD.](#create-an-aad-application) Se necessário, pode obter alguém com as permissões adequadas para criar a aplicação AAD. A pessoa que instala farmBeats tem de ser proprietária do Grupo de Recursos no qual o FarmBeats está a ser instalado.

Pode verificar as suas permissões de acesso no portal Azure seguindo as instruções sobre o controlo de acesso baseado em [funções](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Decidir assinatura e região

Você precisará da ID de assinatura do Azure e da região em que deseja instalar o Azure FarmBeats. Escolha uma das regiões listadas na secção apoiada pelas [Regiões.](#regions-supported)

Tome nota do ID de **subscrição azure** e da **região de Azure.**

### <a name="create-an-aad-application"></a>Criar uma aplicação AAD

A Azure FarmBeats requer a criação e registo de candidaturas do Azure Ative Directory. Para executar com sucesso o script de criação aAD, são necessárias as seguintes permissões -

- Inquilino - Criador de aplicações AAD
- Assinatura - Proprietário

Execute os seguintes passos numa instância Cloud Shell utilizando o ambiente PowerShell. Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Conclua a configuração conforme instruído.

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

4. O script AAD demora cerca de 2 minutos a executar e a saída saem valores no ecrã, bem como a um ficheiro json no mesmo diretório. Se tivesses mais alguém a executar o guião, pede-lhes que partilhem esta produção contigo.

### <a name="create-sentinel-account"></a>Criar conta do Sentinel

A sua configuração Azure FarmBeats permite-lhe obter imagens de satélite da missão satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência Espacial Europeia para a sua quinta. Para configurar esta configuração, é necessário uma conta Sentinel.

Siga as etapas para criar uma conta gratuita com o sentinela:

1. Vá à página [oficial de inscrição.](https://aka.ms/SentinelRegistration)
2. Forneça os detalhes necessários (nome, sobrenome, nome de usuário, senha e ID de email) e preencha o formulário.
3. Um link de verificação é enviado para o ID de e-mail registado. Selecione o link fornecido no email e conclua a verificação.

O seu processo de registo está completo. Tome nota do seu Nome de **Utilizador Sentinel** e **Palavra-passe Sentinela,** uma vez que a verificação também esteja concluída.

## <a name="install"></a>Instalar

Está agora pronto para instalar FarmBeats. Siga os passos abaixo para iniciar a instalação:

1. Inicie sessão no Portal do Azure. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD no qual você deseja instalar o Azure FarmBeats.

2. Vá ao Azure Marketplace dentro do portal e procure **o Azure FarmBeats** no Mercado.

3. Uma nova janela com uma visão geral do Azure FarmBeats é exibida. Selecione **Criar**.

4. Uma nova janela aparece. Conclua o processo de inscrição escolhendo a assinatura, o grupo de recursos e o local corretos para os quais você deseja instalar o Azure FarmBeats.

5. Forneça o endereço de e-mail que deve receber quaisquer alertas de serviço relacionados com o Azure FarmBeats na secção **Dealertas de Serviço FarmBeats.** Selecione **Seguinte** na parte inferior da página para passar para o Separador **Dependências.**

    ![Separador Básico](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copie as entradas individuais da saída do [script AAD](#create-an-aad-application) para as entradas na secção de aplicação AAD.

7. Introduza o nome de utilizador e a palavra-passe da [conta Sentinel](#create-sentinel-account) na secção Conta Sentinel. Selecione **Next** para mover para o separador **Rever + Criar.**

    ![Guia de Dependências](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Assim que os detalhes introduzidos forem validados, selecione **OK**. A página Termos de uso é exibida. Reveja os termos e selecione **Criar** para iniciar a instalação. Será redirecionado para a página onde poderá acompanhar o progresso da instalação.

Uma vez concluída a instalação, pode verificar a instalação e começar a utilizar o portal FarmBeats navegando para o nome do site que forneceu durante a instalação: https://\<FarmBeats-website-name>.azurewebsites.net. Deve ver a interface de utilizador farmBeats com uma opção para criar Quintas.

**Datahub** pode ser encontrado em https://\<FarmBeats-website-name>-api.azurewebsites.net/swagger. Aqui você verá os diferentes objetos FarmBeats API e realizará operações DE REST nas APIs.

## <a name="upgrade"></a>Atualizar

Para atualizar o FarmBeats para a versão mais recente, execute os seguintes passos numa instância cloud Shell utilizando o ambiente PowerShell. O utilizador terá de ser o proprietário da subscrição na qual o FarmBeats está instalado.

Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Conclua a configuração conforme instruído.

1. Descarregue o script de [upgrade](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./update-farmbeats.ps1
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

Para desinstalar o FarmBeats Datahub ou acelerador do Azure, conclua as seguintes etapas:

1. Inicie sessão no portal Azure e **elimine os grupos de recursos** em que estes componentes estão instalados.

2. Vá ao Azure Ative Directory & **elimine a aplicação Azure AD** ligada à instalação Azure FarmBeats.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a instalar o Azure FarmBeats em sua assinatura do Azure. Agora, aprenda a [adicionar utilizadores](manage-users-in-azure-farmbeats.md#manage-users) à sua instância Azure FarmBeats.
