---
title: Crie um teste de Analisador de Internet utilizando | CLI Microsoft Docs
description: Neste artigo, aprenda a criar o seu primeiro teste de Analisador de Internet utilizando o Azure CLI.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a9a9fe93ebe302a76d69249dc56933e1bcc924d1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200092"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Criar um teste de Analisador de Internet utilizando CLI (Pré-visualização)

Existem duas formas de criar um recurso de Analisador de Internet - usando o [portal Azure](internet-analyzer-create-test-portal.md) ou usando o CLI. Esta secção ajuda-o a criar um novo recurso Azure Internet Analyzer utilizando a nossa experiência CLI. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A pré-visualização pública está disponível para ser utilizada globalmente; no entanto, o armazenamento de dados é limitado a *US West 2* durante a pré-visualização.

## <a name="object-model"></a>Modelo de objeto
O Internet Analyzer CLI expõe os seguintes tipos de recursos:
* **Testes** - Um teste compara o desempenho do utilizador final de dois pontos finais da Internet (A e B) ao longo do tempo.
* Perfis - Os **testes** são criados sob um perfil de Analisador de Internet. Os perfis permitem agrupar os testes relacionados; um único perfil pode conter um ou mais testes.
* **Endpoints pré-configurados** - Criámos pontos finais com uma variedade de configurações (regiões, tecnologias de aceleração, etc.). Pode utilizar qualquer um destes pontos finais pré-configurados nos seus testes.
* **Cartões de pontuação** - Um cartão de pontuação fornece resumos rápidos e significativos dos resultados da medição. Consulte a [Interpretação do seu Cartão de Pontuação](internet-analyzer-scorecard.md).
* **Séries tempor** everything - Uma série de tempo mostra como uma métrica muda ao longo do tempo.

## <a name="profile-and-test-creation"></a>Criação de Perfis e Testes
1. Obtenha acesso de pré-visualização do Analisador de Internet seguindo [](internet-analyzer-faq.md)as instruções **de pré-visualização?**
2. [Instale o Azure CLI](/cli/azure/install-azure-cli).
3. Executar o `login` comando para iniciar uma sessão de CLI:
    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.
    Caso contrário, abra uma página do navegador https://aka.ms/devicelogin e introduza o código de autorização exibido no seu terminal.

4. Inicie sessão com as credenciais da sua conta no browser.

5. Selecione o seu ID de subscrição que tenha tido acesso à pré-visualização pública do Internet Analyzer.

    Depois de iniciar sessão, vê uma lista de subscrições associadas à sua conta Azure. A informação de subscrição com `isDefault: true` é a subscrição ativada atualmente após o início de sessão. Para selecionar outra subscrição, utilize o comando [conjunto de conta az](/cli/azure/account#az-account-set) com o ID de subscrição para mudar. Para obter mais informações sobre a seleção de subscrição, consulte [utilizar várias subscrições do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

    Existem formas de iniciar sessão de forma não interativa, que são abordadas em detalhe em [Iniciar sessão com a CLI do Azure](/cli/azure/authenticate-azure-cli).

6. **[Opcional]** Criar um novo Grupo de Recursos Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Instale a extensão do analisador de internet Azure CLI:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Criar um novo perfil de Analisador de Internet:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Lista todos os pontos finais pré-configurados disponíveis para o perfil recém-criado:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Crie um novo teste sob o perfil internetAnalyzer recém-criado:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    O comando acima pressupõe que ambos `www.contoso.com` e `www.microsoft.com` estão hospedando a imagem de um pixel[ (trans.gif](https://fpc.msedge.net/apc/trans.gif)) em caminhos personalizados. Se um caminho de objeto não for especificado explicitamente, o Analisador de Internet usará `/apc/trans.gif` como o caminho do objeto por padrão, que é onde os pontos finais pré-configurados estão hospedando a imagem de um pixel. Note também que o esquema (https/http) não precisa de ser especificado; O Internet Analyzer suporta apenas pontos finais HTTPS, pelo que o HTTPS é assumido.

11. O novo teste deve aparecer no perfil do Analisador de Internet:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Exemplo de saída:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Para começar a gerar medições, o ficheiro JavaScript apontado pelo **scriptFileUri** do teste tem de ser incorporado na sua aplicação Web. As instruções específicas podem ser encontradas na página do [Cliente analisador de Internet incorporado.](internet-analyzer-embed-client.md)

13. Pode monitorizar o progresso do teste mantendo o seu valor de "estado":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Pode verificar os resultados recolhidos do teste gerando timeeries ou cartões de pontuação para o mesmo:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Passos seguintes

* Navegue na [referência CLI do Analisador de Internet](/cli/azure/ext/internet-analyzer/internet-analyzer) para a lista completa de comandos e exemplos de utilização suportados.
* Leia o [Analisador de Internet FAQ.](internet-analyzer-faq.md)
* Saiba mais sobre a incorporação do [Cliente Analisador de Internet](internet-analyzer-embed-client.md) e a criação de um ponto final [personalizado.](internet-analyzer-custom-endpoint.md)