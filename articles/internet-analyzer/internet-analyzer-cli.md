---
title: Criar um teste do Internet Analyzer usando a CLI | Microsoft Docs
description: Neste artigo, saiba como criar seu primeiro teste do Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184272"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Criar um teste do Internet Analyzer usando a CLI (versão prévia)

Há duas maneiras de criar um recurso do Internet Analyzer – usando o [portal do Azure](internet-analyzer-create-test-portal.md) ou usando a CLI. Esta seção ajuda você a criar um novo recurso do Azure Internet Analyzer usando nossa experiência com a CLI. 


> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A visualização pública está disponível para uso global. no entanto, o armazenamento de dados é limitado ao *oeste dos EUA 2* durante a visualização.

## <a name="object-model"></a>Modelo de objeto
A CLI do Internet Analyzer expõe os seguintes tipos de recursos:
* **Testes** – um teste compara o desempenho do usuário final de dois pontos de extremidade da Internet (A e B) ao longo do tempo.
* **Perfis** – os testes são criados em um perfil do Internet Analyzer. Os perfis permitem que os testes relacionados sejam agrupados; um único perfil pode conter um ou mais testes.
* **Pontos de extremidade pré-configurados** – configuramos pontos de extremidade com uma variedade de configurações (regiões, tecnologias de aceleração, etc.). Você pode usar qualquer um desses pontos de extremidade pré-configurados em seus testes.
* **Scorecards** -um scorecard fornece resumos rápidos e significativos dos resultados da medição. Consulte [interpretando seu Scorecard](internet-analyzer-scorecard.md).
* **Série temporal** -uma série temporal mostra como uma métrica muda ao longo do tempo.

## <a name="profile-and-test-creation"></a>Criação de perfil e teste
1. Obtenha acesso à visualização do Internet Analyzer seguindo o **como fazer participar da versão prévia?** instruções das [perguntas frequentes sobre o Azure Internet Analyzer](internet-analyzer-faq.md).
2. [Instale o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Execute o comando `login` para iniciar uma sessão da CLI:
    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela fará isso e carregará uma página de entrada do Azure.
    Caso contrário, abra uma página do navegador em https://aka.ms/devicelogin e insira o código de autorização exibido no terminal.

4. Inicie sessão com as credenciais da sua conta no browser.

5. Selecione sua ID de assinatura que tenha recebido acesso à visualização pública do Internet Analyzer.

    Depois de fazer logon, você verá uma lista de assinaturas associadas à sua conta do Azure. As informações de assinatura com `isDefault: true` é a assinatura ativada no momento após o logon. Para selecionar outra assinatura, use o comando [AZ Account Set](https://docs.microsoft.com/cli/azure/account#az-account-set) com a ID de assinatura para alternar para. Para obter mais informações sobre a seleção de assinatura, consulte [usar várias assinaturas do Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Existem formas de iniciar sessão de forma não interativa, que são abordadas em detalhe em [Iniciar sessão com a CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Opcional]** Crie um novo grupo de recursos do Azure:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Instale a extensão do CLI do Azure Internet Analyzer:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Criar um novo perfil do Internet Analyzer:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Listar todos os pontos de extremidade pré-configurados disponíveis para o perfil recém-criado:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Crie um novo teste no perfil InternetAnalyzer recém-criado:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    O comando acima pressupõe que `www.contoso.com` e `www.microsoft.com` estão hospedando a imagem de um pixel ([Trans. gif](https://fpc.msedge.net/apc/trans.gif)) em caminhos personalizados. Se um caminho de objeto não for especificado explicitamente, o Internet Analyzer usará `/apc/trans.gif` como o caminho do objeto por padrão, que é onde os pontos de extremidade pré-configurados estão hospedando a imagem de um pixel. Observe também que o esquema (HTTPS/HTTP) não precisa ser especificado; O Internet Analyzer só dá suporte a pontos de extremidade HTTPS, portanto, HTTPS é assumido.

11. O novo teste deve aparecer sob o perfil do Internet Analyzer:
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

12. Para começar a gerar medidas, o arquivo JavaScript apontado pelo **scriptFileUri** do teste deve ser inserido em seu aplicativo Web. Instruções específicas podem ser encontradas na página [Inserir cliente do Internet Analyzer](internet-analyzer-embed-client.md) .

13. Você pode monitorar o progresso do teste controlando seu valor de "status":
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Você pode inspecionar os resultados coletados do teste gerando timeseries ou scorecards para ele:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Passos seguintes

* Procure a [referência da CLI do Internet Analyzer](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) para obter a lista completa de comandos e exemplos de uso com suporte.
* Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md).
* Saiba mais sobre como inserir o [cliente do Internet Analyzer](internet-analyzer-embed-client.md) e criar um [ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md). 
