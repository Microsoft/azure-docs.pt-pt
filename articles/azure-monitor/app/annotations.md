---
title: Lançar anotações para insights de aplicações | Microsoft Docs
description: Adicione marcadores de implementação ou construção aos gráficos de exploradores de métricas em Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 9132e65e4705fd9125d97a5e095fe5f0850229a2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011055"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos métricos em Insights de Aplicação

As anotações mostram onde implantaste uma nova construção, ou outros eventos significativos. As anotações facilitam a localização das alterações no desempenho da sua aplicação. Podem ser criados automaticamente pelo sistema de construção de [Gasodutos Azure.](/azure/devops/pipelines/tasks/) Também pode criar anotações para sinalizar qualquer evento que goste, criando-as a partir do PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Liberar anotações com a construção de Gasodutos Azure

As anotações de lançamento são uma característica do serviço Azure Pipelines baseado na nuvem da Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instale a extensão de Anotações (uma vez)

Para poder criar anotações de lançamento, terá de instalar uma das muitas extensões Azure DevOps disponíveis no Visual Studio Marketplace.

1. Inscreva-se no seu projeto [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na página de extensão de [anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) do Mercado do Estúdio Visual, selecione a organização Azure DevOps e, em seguida, selecione **Instalar** para adicionar a extensão à sua organização Azure DevOps.
   
   ![Selecione uma organização Azure DevOps e, em seguida, selecione Instalar.](./media/annotations/1-install.png)
   
Só precisa de instalar a extensão uma vez para a sua organização Azure DevOps. Pode agora configurar anotações de lançamento para qualquer projeto da sua organização.

### <a name="configure-release-annotations"></a>Configurar anotações de libertação

Crie uma chave API separada para cada um dos seus modelos de libertação de Azure Pipelines.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o recurso Application Insights que monitoriza a sua aplicação. Ou se não tiver um, [crie um novo recurso Application Insights](./app-insights-overview.md).
   
1. Abra o separador acesso a **API** e copie o **ID de Insights de Aplicação**.
   
   ![No Acesso API, copie o ID da aplicação.](./media/annotations/2-app-id.png)

1. Numa janela separada do navegador, abra ou crie o modelo de desbloqueio que gere as implementações dos Seus Pipelines Azure.
   
1. **Selecione Adicionar a tarefa** e, em seguida, selecione a tarefa de **anotação de desbloqueio de insights** de aplicação no menu.
   
   ![Selecione Adicionar Tarefa e selecione a anotação de desbloqueio de insights de aplicação.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de anotação de lançamento suporta atualmente apenas agentes baseados no Windows; não será executado em Linux, macOS, ou outros tipos de agentes.
   
1. No **ID da aplicação,** cole o ID de Insights de Aplicação que copiou do separador acesso da **API.**
   
   ![Cole o ID de Insights de Aplicação](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela de acesso a **API** de Informações de Aplicações, selecione **Criar chave API**. 
   
   ![No separador API Access, selecione Criar Chave API.](./media/annotations/5-create-api-key.png)
   
1. Na janela da **chave Create API,** escreva uma descrição, selecione **anotações de escrita** e, em seguida, selecione **'Gerar tecla ' Geração'** Copie a nova chave.
   
   ![Na janela da chave Create API, escreva uma descrição, selecione anotações de escrita e, em seguida, selecione Gerar tecla.](./media/annotations/6-create-api-key.png)
   
1. Na janela do modelo de libertação, no separador **Variáveis,** **selecione Adicionar** para criar uma definição variável para a nova chave API.

1. In **Name**, insira `ApiKey` , e em **Valor**, cole a chave API que copiou a partir do separador **Acesso API.**
   
   ![No separador Azure DevOps Variables, selecione Adicionar, nomeie a variável ApiKey e cole a tecla API em Valor.](./media/annotations/7-paste-api-key.png)
   
1. **Selecione Guarde** na janela do modelo de libertação principal para guardar o modelo.


   > [!NOTE]
   > Os limites para as chaves API são descritos na documentação de [limites da taxa de API](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)REST .

## <a name="view-annotations"></a>Ver anotações


   > [!NOTE]
   > As anotações de libertação não estão atualmente disponíveis no painel métrica de Insights de Aplicação

Agora, sempre que utilizar o modelo de libertação para implementar uma nova versão, é enviada uma anotação para o Application Insights. As anotações podem ser vistas nos seguintes locais:

O **painel de utilização** onde também tem a capacidade de criar anotações de libertação manual:

![Screenshot do gráfico de barras com número de visitas de utilizador exibidas durante um período de horas. Anotações de libertação aparecem como marcas de verificação verdes acima do gráfico indicando o momento em que ocorreu uma libertação](./media/annotations/usage-pane.png)

Em qualquer consulta de livro baseada em log-based onde a visualização exibe tempo ao longo do eixo x.

![Screenshot do painel de livros com consulta baseada em registos de séries de tempo com anotações exibidas](./media/annotations/workbooks-annotations.png)

Para ativar anotações no seu livro, vá a **Definições Avançadas** e selecione **Mostrar anotações**.

![Screenshot do menu Definições Avançadas com as palavras mostram anotações realçadas com uma marca de verificação ao lado da definição para o ativar.](./media/annotations/workbook-show-annotations.png)

Selecione qualquer marcador de anotação para abrir detalhes sobre o lançamento, incluindo o solicitador, ramo de controlo de fonte, oleoduto de libertação e ambiente.

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas a partir de PowerShell
Pode utilizar o script CreateReleaseAnnotation PowerShell para criar anotações a partir de qualquer processo que queira, sem utilizar DevOps Azure.

> [!IMPORTANT]
> Se estiver a utilizar o PowerShell 7.1, adicione `-SkipHttpErrorCheck` no final da linha 26. Por exemplo: `$request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore -SkipHttpErrorCheck`.

1. Faça uma cópia local de CreateReleaseAnnotation.ps1:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Utilize os passos no procedimento anterior para obter o ID do seu Application Insights e criar uma chave API a partir do separador De **Acesso API** de Informações de Aplicação.
   
1. Ligue para o script PowerShell com o seguinte código, substituindo os espaços reservados com ângulos pelos seus valores. São `-releaseProperties` opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Pode modificar o script, por exemplo, para criar anotações para o passado.


## <a name="next-steps"></a>Passos seguintes

* [Criar itens de trabalho](./diagnostic-search.md#create-work-item)
* [Automatização com o PowerShell](./powershell.md)
