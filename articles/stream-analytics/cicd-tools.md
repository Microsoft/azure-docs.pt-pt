---
title: Automatizar construções, testes e implementações de um trabalho Azure Stream Analytics utilizando ferramentas CI/CD
description: Este artigo descreve como usar ferramentas CI/CD do Azure Stream Analytics para construir, testar e implementar um projeto Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 2dbb491e77f132daf7b432f27705eba9e3e3cd3c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036967"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatizar construções, testes e implementações de um trabalho Azure Stream Analytics utilizando ferramentas CI/CD

Pode utilizar o pacote Azure Stream Analytics CI/CD npm para construir, testar e implementar automaticamente os seus projetos Azure Stream Analytics Visual Studio Code ou Visual Studio. Os projetos podem ser criados usando ferramentas de desenvolvimento ou podem ser exportados a partir de empregos stream analytics existentes. Este artigo descreve como utilizar o pacote npm com qualquer sistema CI/CD. Para a implementação com os gasodutos Azure, consulte [use Azure DevOps para criar um pipeline CI/CD para um trabalho de Stream Analytics](set-up-cicd-pipeline.md).

## <a name="installation"></a>Instalação

Pode [descarregar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) usando o `npm install -g azure-streamanalytics-cicd` comando. Recomendamos a utilização do comando, que também pode ser utilizado numa tarefa de script PowerShell ou Azure CLI de um gasoduto de construção em **Gasodutos Azure**.

## <a name="build-the-project"></a>Compilar o projeto

O pacote **asa-streamanalytics-cicd** npm fornece as ferramentas para gerar modelos de Gestor de Recursos Azure de [projetos](./quick-create-visual-studio-code.md) de Stream Analytics Visual Studio Code ou [projetos de Estúdio Visual](stream-analytics-quick-create-vs.md). Também pode utilizar o pacote npm no Windows, macOS e Linux sem instalar o Visual Studio Code ou o Visual Studio.

Depois de instalar o pacote, utilize o seguinte comando para construir os seus projetos Stream Analytics.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

O comando *de construção* faz uma verificação de sintaxe de palavra-chave e produz o modelo Azure Resource Manager.

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho absoluto do **asaproj.jsem** arquivo para o seu projeto Visual Studio Code ou **[O nome do seu projeto].asaproj** para o projeto Visual Studio. |
| `-outputPath` | O caminho da pasta de saída para modelos de gestor de recursos Azure. Se não for especificado, os modelos serão colocados no diretório atual. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Quando um projeto Stream Analytics se constrói com sucesso, gera os seguintes dois ficheiros sob a pasta de saída:

* Arquivo de modelo do gestor de recursos Azure

   `[ProjectName].JobTemplate.json`

* Arquivo de parâmetro do Gestor de Recursos Azure

   `[ProjectName].JobTemplate.parameters.json`

Os parâmetros padrão no parameters.jsno ficheiro são das definições do seu projeto Visual Studio Code ou Visual Studio. Se pretender implantar-se noutro ambiente, substitua os parâmetros em conformidade.

Os valores predefinidos para todas as credenciais são **nulos.** É-lhe exigido que desloque os valores antes de se deslocar para Azure.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Para utilizar a Identidade Gerida para a Azure Data Lake Store Gen1 como um lavatório de saída, precisa de fornecer acesso ao principal do serviço usando o PowerShell antes de se deslocar para a Azure. Saiba mais sobre como [implementar a ADLS Gen1 com identidade gerida com o modelo de Gestor de Recursos.](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)

## <a name="local-run"></a>Execução local

Se o seu projeto tiver especificado ficheiros de entrada local, pode executar um script Stream Analytics localmente utilizando o `localrun` comando.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsem** arquivo para o seu projeto Visual Studio Code ou **[O nome do seu projeto].asaproj** para o projeto Visual Studio. |
| `-outputPath` | O caminho da pasta de saída. Se não for especificado, os ficheiros de resultados de saída serão colocados no diretório atual. |
| `-customCodeZipFilePath` | O caminho do ficheiro zip para código personalizado C#, como um UDF ou um desserializador, se forem utilizados. Embale os DLLs num ficheiro zip e especifique este caminho. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF só funciona no Windows.

## <a name="automated-test"></a>Teste automatizado

Pode utilizar o pacote ci/CD npm para configurar e executar testes automatizados para o seu script Stream Analytics.

### <a name="add-a-test-case"></a>Adicione uma caixa de teste

Os casos de teste são descritos num ficheiro de configuração de teste. Para começar, utilize o `addtestcase` comando para adicionar um modelo de caixa de teste ao ficheiro de configuração de teste. Se o ficheiro de configuração de teste não existir, um é criado por padrão.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsem** arquivo para o seu projeto Visual Studio Code ou **[O nome do seu projeto].asaproj** para o projeto Visual Studio. |
| `-testConfigPath` | O caminho do ficheiro de configuração de teste. Se não for especificado, o ficheiro será pesquisado em **\teste** sob o diretório atual do **asaproj.jsem** ficheiro, com o nome do ficheiro predefinido **testConfig.jsem**. Um novo ficheiro será criado se não existir. |

> [!NOTE]
> O `Script` valor dotestConfig.jsgerado em arquivo **destina-se** apenas a fornecer o contexto; Não é usado na lógica de testes. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Se o ficheiro de configuração de teste estiver vazio, o seguinte conteúdo será escrito no ficheiro. Caso contrário, é adicionado um caso de teste à matriz de **TestCases**. As configurações de entrada necessárias são preenchidas automaticamente de acordo com os ficheiros de configuração de entrada, se existirem. Caso contrário, os valores predefinidos são configurados. **FilePath** de cada entrada e saída esperada deve ser especificado antes de executar o teste. Pode modificar a configuração manualmente.

Se pretender que a validação do teste ignore uma determinada saída, desa um campo **necessário** dessa saída esperada para **falso**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> Atualmente, o único valor permitido para o `ScriptType` elemento é , que é também o valor `InputMock` padrão. Se o definir para qualquer outro valor, é ignorado e o valor padrão `InputMock` () é usado. 

### <a name="run-a-unit-test"></a>Fazer um teste de unidade

Pode utilizar o seguinte comando para executar vários casos de teste para o seu projeto. Um resumo dos resultados do teste é gerado na pasta de saída. O processo sai com o código **0** para todos os testes aprovados; **-1,** para exceção, ocorreu; **-2** para os testes falharam.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parâmetro | Descrição |
|---|---|
| `-project` | O caminho do **asaproj.jsem** arquivo para o seu projeto Visual Studio Code ou **[O nome do seu projeto].asaproj** para o projeto Visual Studio. |
| `-testConfigPath` | O caminho para o ficheiro de configuração de teste. Se não for especificado, o ficheiro será pesquisado em **\teste** sob o diretório atual do **asaproj.jsem** ficheiro, com o nome do ficheiro predefinido **testConfig.jsem**.
| `-outputPath` | O caminho da pasta de saída do resultado do teste. Se não for especificado, os ficheiros de resultados de saída serão colocados no diretório atual. |
| `-customCodeZipFilePath` | O caminho do ficheiro postal para código personalizado, como um UDF ou um desserializador, se forem utilizados. |

Quando todos os testes estiverem terminados, um resumo dos resultados do teste no formato JSON é gerado na pasta de saída. O ficheiro de resumo é nomeado **testResultSummary.jsem**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Implementar no Azure

Pode utilizar o modelo do Gestor de Recursos Azure e os ficheiros de parâmetros gerados a partir de Build para [implementar o seu trabalho para a Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Passos seguintes

* [Integração contínua e implantação contínua para Azure Stream Analytics](cicd-overview.md)
* [Configurar o pipeline CI/CD para o trabalho de Stream Analytics utilizando gasodutos Azure](set-up-cicd-pipeline.md)
