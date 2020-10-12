---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar a Azure FarmBeats na sua subscrição Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79479564"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar a Azure FarmBeats na sua subscrição Azure.

A azure FarmBeats é uma oferta de negócios disponíveis no Azure Marketplace. Permite a agregação de conjuntos de dados agrícolas entre os fornecedores e a geração de insights accuíveis. O Azure FarmBeats fá-lo permitindo-lhe construir modelos de inteligência artificial (IA) ou machine learning (ML) baseados em conjuntos de dados fundidos. Os dois principais componentes da Azure FarmBeats são:

- **Datahub**: Uma camada de API que permite agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre diferentes fornecedores.

- **Acelerador**: Aplicação web que é construída em cima do Datahub. Inicia o desenvolvimento e visualização do seu modelo. O acelerador utiliza APIs Azure FarmBeats para demonstrar a visualização de dados de sensores ingeridos como gráficos e visualização da saída do modelo como mapas.

## <a name="general-information"></a>Informações gerais

### <a name="components-installed"></a>Componentes instalados

Quando instala o Azure FarmBeats, os seguintes recursos são a provisionados na sua subscrição Azure:

| Recursos Azure Instalados  | Componente Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Acelerador de & Datahub      |
| Serviço de Aplicações     |     Acelerador de & Datahub     |
| Plano do Serviço de Aplicações   | Acelerador de & Datahub  |
| Conexão API    |  Datahub       |
| Cache do Azure para Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acelerador de & Datahub      |
| Conta do Azure Batch    | Datahub   |
| Azure Key Vault |  Acelerador de & Datahub        |
| Conta Azure Maps       |     Acelerador    |
| Espaço de nome do centro de eventos    |     Datahub      |
| Aplicação Lógica      |  Datahub       |
| Conta de Armazenamento      |     Acelerador de & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo da Azure FarmBeats é um agregado do custo dos serviços Azure subjacentes. As informações sobre preços dos serviços Azure podem ser calculadas utilizando [a Calculadora de Preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total variará em função da utilização. O custo constante do estado para os dois componentes é:

- Datahub - menos de $10 por dia
- Acelerador - menos de $2 por dia

### <a name="regions-supported"></a>Regiões apoiadas

Atualmente, a Azure FarmBeats é apoiada em ambientes de nuvem pública nas seguintes regiões:

- Leste da Austrália
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Oeste
- E.U.A. Oeste 2
- Europa do Norte
- Europa Ocidental
- Ásia Leste
- Ásia do Sul

### <a name="time-taken"></a>Tempo tomado

Toda a configuração do Azure FarmBeats, incluindo a preparação e instalação levará menos de uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Terá de completar os seguintes passos antes de iniciar a instalação real do Azure FarmBeats:

### <a name="verify-permissions"></a>Verificar permissões

Você precisará das seguintes permissões no inquilino Azure para instalar Azure FarmBeats:

- Inquilino - Criador de aplicativos AAD
- Assinatura - Proprietário
- Grupo de Recursos em que o FarmBeats está a ser instalado - Proprietário

As duas primeiras permissões são necessárias para criar o passo [de aplicação da AAD.](#create-an-aad-application) Se necessário, pode obter alguém com as permissões apropriadas para criar a aplicação AAD.

A pessoa que gere os FarmBeats instalados no mercado tem de ser proprietária do Grupo de Recursos no qual o FarmBeats está a ser instalado. Para os proprietários de subscrições, isto acontece automaticamente quando o Grupo de Recursos é criado. Para outros, por favor, crie o Grupo de Recursos e peça ao proprietário da Subscrição para torná-lo proprietário do Grupo de Recursos.

Pode verificar as suas permissões de acesso no portal Azure seguindo as instruções sobre o controlo de acesso baseado em [funções](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Decida a subscrição e a região

Você precisará do ID de assinatura Azure e da região onde você quer instalar Azure FarmBeats. Escolha uma das regiões listadas na secção [de apoio às Regiões.](#regions-supported)

Tome nota do ID de **assinatura Azure** e da **Região Azure.**

### <a name="create-an-aad-application"></a>Criar uma aplicação AAD

A Azure FarmBeats requer a criação e registo de pedidos de diretório azure ative. Para executar com sucesso o script de criação AAD, são necessárias as seguintes permissões:

- Inquilino - Criador de aplicativos AAD
- Assinatura - Proprietário

Executar os seguintes passos numa instância Cloud Shell utilizando o ambiente PowerShell. Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Complete a configuração conforme instruído.

1. Descarregue o script do [gerador de aplicativos AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Por predefinição, o ficheiro é descarregado para o seu diretório de casa. Navegue para o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. O script pede as seguintes três entradas:

    - **Nome do site FarmBeats**: Este é o prefixo URL único para a sua aplicação web FarmBeats. Caso o prefixo já esteja tomado, o script será errar. Uma vez instalado, a sua implantação FarmBeats estará acessível a partir de https:// \<FarmBeats-website-name> .azurewebsites.net e as APIs swagger estarão em https:// \<FarmBeats-website-name> -api.azurewebsites.net

    - **ID de login Azure**: Forneça ID de login Azure para o utilizador que deseja ser adicionado como administrador de FarmBeats. Este utilizador pode então conceder acesso à aplicação web FarmBeats a outros utilizadores. O ID de login é geralmente do formulário john.doe@domain.com . Azure UPN também é apoiado.

    - **ID de assinatura**: Este é o ID da subscrição em que pretende instalar a Azure FarmBeats

5. O script AAD demora cerca de 2 minutos a executar e os valores de saídas no ecrã, bem como a um ficheiro json no mesmo diretório. Se tiveres mais alguém a publicar o guião, pede-lhes que partilhem esta produção contigo.

### <a name="create-sentinel-account"></a>Criar conta Sentinel

A sua configuração Azure FarmBeats permite-lhe obter imagens de satélite da missão satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência Espacial Europeia para a sua quinta. Para configurar esta configuração, você precisa de uma conta Sentinel.

Siga os passos para criar uma conta gratuita com o Sentinel:

1. Vá à página oficial [de inscrição.](https://aka.ms/SentinelRegistration)
2. Forneça os detalhes necessários (nome próprio, apelido, nome de utilizador, senha e ID de e-mail) e preencha o formulário.
3. Um link de verificação é enviado para o ID de e-mail registado. Selecione o link fornecido no e-mail e complete a verificação.

O seu processo de registo está completo. Tome nota do seu nome de **utilizador Sentinel** e **Password Sentinel,** uma vez que a verificação também esteja concluída.

## <a name="install"></a>Instalar

Está agora pronto para instalar o FarmBeats. Siga os passos abaixo para iniciar a instalação:

1. Inicie sessão no Portal do Azure. Selecione a sua conta no canto superior direito e mude para o inquilino AZure AD onde pretende instalar o Azure FarmBeats.

2. Vá ao Azure Marketplace dentro do portal e procure **a Azure FarmBeats** no Marketplace.

3. Uma nova janela com uma visão geral do Azure FarmBeats aparece. Selecione **Criar**.

4. Aparece uma nova janela. Complete o processo de inscrição escolhendo a subscrição correta, o grupo de recursos e a localização a que pretende instalar o Azure FarmBeats.

5. Forneça o endereço de e-mail que deve receber quaisquer alertas de serviço relacionados com a Azure FarmBeats na secção **Alertas de Serviço FarmBeats.** Selecione **Seguinte** na parte inferior da página para passar para o Separador **Dependências.**

    ![Separador básico](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copie as entradas individuais da saída do [script AAD](#create-an-aad-application) para as entradas na secção de aplicação AAD.

7. Introduza o nome de utilizador da [conta Sentinel](#create-sentinel-account) e a palavra-passe na secção Conta Sentinela. Selecione **Seguinte** para mover-se para o **separador 'Rever + Criar'.**

    ![Separador de Dependências](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Uma vez validados os detalhes introduzidos, selecione **OK**. A página termos de uso aparece. Reveja os termos e **selecione Criar** para iniciar a instalação. Será redirecionado para a página onde pode acompanhar o progresso da instalação.

Uma vez concluída a instalação, pode verificar a instalação e começar a utilizar o portal FarmBeats navegando para o nome do site que forneceu durante a instalação: https:// \<FarmBeats-website-name> .azurewebsites.net. Você deve ver a interface de utilizador FarmBeats com uma opção para criar Fazendas.

**Datahub** pode ser encontrado em \<FarmBeats-website-name> https://-api.azurewebsites.net/swagger. Aqui você verá os diferentes objetos API FarmBeats e realizará operações REST nas APIs.

## <a name="upgrade"></a>Atualizar

Para atualizar os FarmBeats para a versão mais recente, execute os seguintes passos numa instância Cloud Shell utilizando o ambiente PowerShell. O utilizador terá de ser o proprietário da subscrição na qual o FarmBeats está instalado.

Os utilizadores pela primeira vez serão solicitados a selecionar uma subscrição e criar uma conta de armazenamento. Complete a configuração conforme instruído.

1. Descarregue o [script de upgrade](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Por predefinição, o ficheiro é descarregado para o seu diretório de casa. Navegue para o diretório.

    ```azurepowershell-interactive
        cd
    ```

3. Executar o script de upgrade

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

O caminho para input.jsno ficheiro é opcional. Se não for especificado, o script pedirá todas as entradas necessárias. A atualização deve terminar em cerca de 30 minutos.

## <a name="uninstall"></a>Desinstalar

Para desinstalar o Datahub ou Acelerador Azure FarmBeats, complete os seguintes passos:

1. Faça login no portal Azure e **elimine os grupos de recursos** em que estes componentes estão instalados.

2. Vá ao Azure Ative Directory & **elimine a aplicação AZure AD** ligada à instalação Azure FarmBeats.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a instalar o Azure FarmBeats na sua subscrição Azure. Agora, aprenda a [adicionar utilizadores](manage-users-in-azure-farmbeats.md#manage-users) à sua instância Azure FarmBeats.
