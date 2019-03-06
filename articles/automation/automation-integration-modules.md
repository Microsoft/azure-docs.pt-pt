---
title: Criar um módulo de integração da automatização do Azure
description: Tutorial que o orienta através da criação, teste e exemplo de utilização dos módulos de integração na Automatização do Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2800385581ea3f08fc1013e980f8c133a2f7241e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443737"
---
# <a name="azure-automation-integration-modules"></a>Módulos de Integração da Automatização do Azure

O PowerShell é a tecnologia fundamental por trás da Automatização do Azure. Uma vez que a Automatização do Azure é baseada no PowerShell, os módulos do PowerShell são essenciais para a extensibilidade da Automatização do Azure. Neste artigo, irá aprender os detalhes de utilização da automatização do Azure de módulos do PowerShell, conhecido como módulos de integração. Também aprenderá as práticas recomendadas para criar seus próprios módulos do PowerShell para se certificar de que funcionam como módulos de integração na automatização do Azure. 

## <a name="what-is-a-powershell-module"></a>O que é um Módulo do PowerShell?

Um módulo do PowerShell é um grupo de cmdlets do PowerShell, como **Get-Date** ou **Copy-Item**, que podem ser utilizados a partir de:

* A consola do PowerShell
* scripts
* Fluxos de trabalho
* runbooks
* Recursos de DSC

Todas as funcionalidades do PowerShell está exposta através de cmdlets e recursos de DSC. Cada cmdlet e os recursos de DSC é apoiada por um módulo do PowerShell, muitos dos quais são enviados juntamente com o PowerShell. Por exemplo, o **Get-Date** cmdlet faz parte do `Microsoft.PowerShell.Utility` módulo do PowerShell, e **Copy-Item** cmdlet faz parte do `Microsoft.PowerShell.Management` módulo do PowerShell e o recurso de pacote DSC faz parte dos Módulo do PSDesiredStateConfiguration PowerShell. Ambos estes módulos são enviados com o PowerShell. Muitos módulos do PowerShell não são fornecidos como parte do PowerShell. Estes módulos são distribuídos com produtos de primeira ou de terceiros ou em locais como a galeria do PowerShell. Os módulos são úteis porque tornam as tarefas complexas mais simples através da funcionalidade encapsulada.  Pode saber mais sobre os [módulos do PowerShell no MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>O que é um Módulo de Integração da Automatização do Azure?

Um módulo de integração não é diferente de um módulo do PowerShell. É, simplesmente, um módulo do PowerShell que, opcionalmente, contém um ficheiro adicional - um ficheiro de metadados que especifica um tipo de ligação da Automatização do Azure a utilizar com os cmdlets do módulo em runbooks. Módulos do PowerShell podem ser importados para a automatização do Azure para tornar os seus cmdlets disponíveis para utilização nos runbooks e os recursos de DSC disponíveis para utilização nas configurações de DSC. Nos bastidores, a automatização do Azure armazena estes módulos e no e tempo de execução da tarefa de compilação de DSC da tarefa de runbook, carrega-os para as sandboxes de automatização do Azure onde executar runbooks e configurações de DSC compilam. Quaisquer recursos de DSC nos módulos também são automaticamente colocados no servidor de solicitação de DSC de automatização. Eles podem ser solicitados pelas máquinas quando estas são aplicadas as configurações de DSC.  

Um número de módulos do Azure PowerShell que enviamos prontos a utilizar na automatização do Azure. Mas é possível importar os módulos do PowerShell para qualquer sistema, serviço ou ferramenta que pretende integrar.

> [!NOTE]
> Determinados módulos são fornecidos como **módulos globais** no serviço de automatização. Estes módulos globais estão disponíveis para si quando cria uma conta de automatização e atualizamos por vezes, que automaticamente envia-os à sua conta de automatização. Se não quiser que eles sejam atualizados automaticamente, pode sempre importar o módulo por conta própria e que tem precedência sobre a versão do módulo global desse módulo que incluímos no serviço.

O formato no qual importa um pacote do Módulo de Integração é um ficheiro comprimido com o mesmo nome que o módulo e uma extensão .zip. Contém o módulo do Windows PowerShell e quaisquer ficheiros de suporte, incluindo um ficheiro de manifesto (. psd1) se o módulo tiver um.

Se o módulo tiver de ter um tipo de ligação da Automatização do Azure, também tem de conter um ficheiro com o nome `<ModuleName>-Automation.json` que especifique as propriedades do tipo de ligação. Este é um ficheiro json, que é colocado dentro da pasta do módulo do seu ficheiro. zip comprimido. Este ficheiro contém os campos de um **ligação** que é necessário para ligar ao sistema ou serviço que o módulo representa. Esta configuração acaba de criar um tipo de ligação na automatização do Azure. Ao utilizar este ficheiro, pode definir os nomes de campo, os tipos e se os campos devem ser encriptados e/ou opcionais para o tipo de ligação do módulo. O exemplo seguinte é um modelo no formato de ficheiro de json:

```json
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Se tiver implementado o Service Management Automation e criado pacotes de módulos de integração para os runbooks de automatização, este modelo deve parecer familiar para.

## <a name="authoring-best-practices"></a>Melhores Práticas de Criação

Apesar dos módulos de integração são módulos do PowerShell, ainda há uma série de coisas que recomendamos que considere quando cria um módulo do PowerShell para utilização na automatização do Azure. Algumas recomendações são úteis para que os módulos funcionem bem no fluxo de trabalho do PowerShell.

1. Inclua um resumo, uma descrição e um URI de ajuda para cada cmdlet no módulo. No PowerShell, pode definir determinadas informações de ajuda para os cmdlets para permitir ao utilizador receber ajuda sobre a utilização dos mesmos com o cmdlet **Get-Help**. Por exemplo, eis como pode definir uma sinopse e URI de ajuda para um módulo do PowerShell escrito num ficheiro. psm1. 
   
    ```powershell
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='https://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```

   Fornecer estas informações mostra esta ajuda relativa à utilização a **Get-Help** cmdlet na consola do PowerShell. Essas informações também são expostas na automatização do Azure.  Por exemplo, quando inserir atividades durante a criação de um runbook. Clicar em "Ver ajuda detalhada", abrirá o URI de ajuda noutro separador do browser que estiver a utilizar para aceder a automatização do Azure.

   ![Ajuda do Módulo de Integração](media/automation-integration-modules/automation-integration-module-activitydesc.png)

2. Se o módulo for executado relativamente a um sistema remoto:

   1. Deve conter um ficheiro de metadados do Módulo de Integração que defina as informações necessárias para estabelecer ligação a esse sistema remoto, ou seja, o tipo de ligação.
   2. Cada cmdlet no módulo deve ser capaz de considerar um objeto de ligação (uma instância desse tipo de ligação) como um parâmetro.  

    Os cmdlets no módulo tornam-se mais fáceis de utilizar na Automatização do Azure se permitir a passagem de um objeto com os campos do tipo de ligação como um parâmetro para o cmdlet. Isso permite que os utilizadores mapear os parâmetros do recurso de ligação para parâmetros correspondentes do cmdlet sempre que chamarem um cmdlet.

    Com base no exemplo de runbook acima, utiliza um recurso de ligação do Twilio denominado CorpTwilio para aceder ao Twilio e devolver todos os números de telefone na conta.  Observe como se mapeia os campos da ligação para os parâmetros do cmdlet?

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Uma forma melhor e mais fácil abordar este comportamento consiste na passagem direta do objeto de ligação para o cmdlet-

    ```powershell
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```

    Pode ativar o comportamento semelhante ao exemplo anterior para os cmdlets ao permitir que aceitem um objeto de ligação diretamente como um parâmetro, em vez de apenas os campos de ligação para os parâmetros. Normalmente, quer um parâmetro definido para cada um, para que um usuário não utilizar a automatização do Azure possa chamar os seus cmdlets sem construir uma tabela de hash para atuar como o objeto de ligação. O conjunto de parâmetros **SpecifyConnectionFields**, é usado para passar a ligação de propriedades de campo individualmente. **UseConnectionObject** permite a passagem direta da ligação. Como pode ver, o cmdlet Send-TwilioSMS no [módulo Twilio do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) permite a passagem de qualquer uma das formas:

    ```powershell
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='https://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```

3. Defina o tipo de saída para todos os cmdlets no módulo. Definir um tipo de saída para um cmdlet permite que o IntelliSense, no momento da conceção, o ajude a determinar as propriedades de saída do cmdlet para uma utilização durante a criação. É especialmente útil durante a automação criação gráfica de runbooks, onde o conhecimento de tempo de design é fundamental para uma experiência de utilizador fácil com o seu módulo.

   ![Tipo de Saída Gráfica do Runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)

   Este comportamento é semelhante a funcionalidade de "escrita antecipada" da saída de um cmdlet no ISE do PowerShell sem ter de executá-lo.

   ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)

4. Cmdlets no módulo não deve demorar tipos de parâmetros de objetos complexos. O Fluxo de Trabalho do PowerShell é diferente do PowerShell no sentido em que armazena tipos complexos no formato de serialização anulada. Tipos primitivos são como primitivos, mas tipos complexos converter em suas versões de serialização anuladas, o que são, essencialmente, matrizes de propriedades. Por exemplo, se tiver utilizado o cmdlet **Get-Process** num runbook (ou um Fluxo de Trabalho do PowerShell), iria devolver um objeto do tipo [Deserialized.System.Diagnostic.Process] e não o tipo esperado [System.Diagnostic.Process]. Este tipo tem as mesmas propriedades que o tipo sem serialização anulada, mas nenhum dos métodos. E se tentar passar este valor como um parâmetro para um cmdlet, onde o cmdlet espera um valor de [Diagnostic] para este parâmetro, receberá o erro seguinte: *Não é possível processar a transformação do argumento no parâmetro "processo". Erro: "Não é possível converter o valor de"Process (CcmExec)"do tipo"Deserialized"no tipo"Diagnostics".*   Isto acontece porque existe um erro de correspondência entre o tipo esperado [Diagnostic] e o tipo [deserialized] especificado. Contornar este problema é garantir que os cmdlets do seu módulo não consideram tipos complexos como parâmetros. Eis a forma errada de o fazer.

    ```powershell
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ```

    A maneira correta é dar um primitivo que é utilizada internamente pelo cmdlet para captar o objeto complexo e utilizá-lo. Uma vez que os cmdlets são executados no contexto do PowerShell, e não no Fluxo de Trabalho do PowerShell, dentro do cmdlet $process torna-se o tipo [System.Diagnostic.Process] correto.  

    ```powershell
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```

   Recursos de ligação nos runbooks são tabelas hash, que é um tipo complexo, e ainda que estas tabelas hash parecem conseguir passar para os cmdlets para seus "parâmetro de ligação com o perfeitamente, sem exceção de transmissão. Tecnicamente, alguns tipos de PowerShell conseguem transmitir corretamente a partir do respetivo formato serializado para o formato de serialização anulada e, por conseguinte, podem passar para os cmdlets para os parâmetros que aceitam o tipo sem serialização anulada. A tabela hash também está incluída. É possível para tipos definidos de um autor de módulos sejam implementados de forma a poderem corretamente anular a serialização, bem, mas existem algumas desvantagens a serem considerados. O tipo tem de ter um construtor predefinido, ter todas as suas propriedades públicas e ter um PSTypeConverter. No entanto, para tipos já definidos que o autor do módulo não é proprietário, não é possível para "corrigir", por conseguinte, a recomendação para evitar tipos complexos para todos os parâmetros. Sugestão de criação de Runbooks: Se precisar de seus cmdlets tirar um parâmetro de tipo complexo, a solução em fluxos de trabalho do PowerShell, é moldar o cmdlet que gera o tipo complexo e o cmdlet que consome o tipo complexo na mesma atividade do InlineScript. Uma vez que o InlineScript executa o respetivo conteúdo como o PowerShell e não como o Fluxo de Trabalho do PowerShell, o cmdlet que gera o tipo complexo produziria esse tipo correto e não o tipo complexo no formato de serialização anulada.

5. Crie todos os cmdlets no módulo sem monitorização de estado. O Fluxo de Trabalho do PowerShell executa cada cmdlet chamado no fluxo de trabalho numa sessão diferente. Isso significa que qualquer cmdlet que dependa do Estado da sessão criado / modificado por outros cmdlets no mesmo módulo não funcionará nos runbooks do fluxo de trabalho do PowerShell.  Eis um exemplo de o que não deve fazer.
   
    ```powershell
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```

6. O módulo deve estar totalmente incluído num pacote compatível com Xcopy. Módulos de automatização do Azure são distribuídos nas sandboxes da automatização quando os runbooks precisam executar. Os módulos tem de funcionar independentemente do anfitrião que estão a executar. Deve ser capaz de zipar e mover de um pacote do módulo e tê-lo a funcionar normalmente quando importados para o ambiente do PowerShell de outro anfitrião. Por ordem para que isso aconteça, o módulo não deve depender de quaisquer ficheiros fora da pasta do módulo. Esta pasta é a pasta que é zipada quando o módulo é importado para a automatização do Azure. O módulo também não deve depender quaisquer definições de registo única num anfitrião, como essas configurações definidas quando um produto é instalado. Se não for seguida esta melhor prática, o módulo não será utilizável na automatização do Azure.  

7. Se a referência à [módulos do Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) no seu módulo, certifique-se de que não está a referenciar também `AzureRM`. O `Az` módulo não pode ser utilizado em conjunto com o `AzureRM` módulos. `Az` é suportado em runbooks, mas não são importados por predefinição. Para saber mais sobre o `Az` módulos e considerações a ter em conta, consulte [suporte de módulo de Az na automatização do Azure](az-modules.md).

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre a criação de Módulos do PowerShell, veja o artigo [Escrever um Módulo do Windows PowerShell](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)
