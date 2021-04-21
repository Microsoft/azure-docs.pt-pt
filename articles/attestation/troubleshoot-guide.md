---
title: Guia de resolução de problemas do Azure Attestation
description: Guia de tiro de problemas para os problemas comumente observados
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d3e34bee3d0f1420b379638389e6fad0a2fed60
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831569"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Guia de resolução de problemas do Microsoft Azure Attestation

O manuseamento de erros no Azure Attestation é implementado de acordo com [as diretrizes da Microsoft REST API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). A resposta de erro devolvida pela Azure Attestation APIs contém pares de código e nome/valor HTTP com os nomes "código" e "mensagem". O valor do "código" é legível pelo homem e é um indicador do tipo de erro. O valor da "mensagem" pretende ajudar o utilizador e fornece detalhes de erro.

Se o seu problema não for abordado neste artigo, também pode submeter um pedido de apoio do Azure na página de suporte do [Azure](https://azure.microsoft.com/support/options/).

Abaixo estão alguns exemplos dos erros devolvidos pela Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP-401 : Exceção não autorizada

### <a name="http-status-code"></a>Código de estado de HTTP
401

**Código de erro** Não autorizado

**Exemplos de cenário**
  - Incapaz de gerir políticas de atestação, uma vez que o utilizador não é atribuído com funções adequadas
  - Não é possível gerir os signatários da política de atestado, uma vez que o utilizador não é atribuído com funções adequadas

Função do utilizador com o leitor tentando editar uma política de atestado no PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Passos de resolução de problemas**

Para gerir as políticas, um utilizador AD Azure requer as seguintes permissões para "Ações":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Para executar estas ações, um utilizador AD Azure deve ter o papel de "Attestation Contributor" no prestador de atestado. Estas permissões também podem ser herdadas com funções como "Proprietário" (permissões wildcard), "Contribuinte" (permissões wildcard) no grupo de subscrição/recursos.  

Para ler políticas, um utilizador AZure AD requer a seguinte permissão para "Ações":
- Microsoft.Attestation/attestationProviders/attestation/read

  Para realizar esta ação, um utilizador AD Azure deve ter o papel de "Attestation Reader" no fornecedor de atestado. A permissão de leitura também pode ser herdada com papéis como "Reader" (permissões wildcard) no grupo de subscrição/recursos.  

Para verificar as funções no PowerShell, verifique os passos abaixo:

a. Lançar PowerShell e iniciar sessão no Azure através do cmdlet "Connect-AzAccount"

b. Por favor, consulte [aqui](../role-based-access-control/role-assignments-list-powershell.md) as orientações para verificar a sua atribuição de funções Azure no fornecedor de atestado

c. Se não encontrar uma tarefa de função apropriada, siga as instruções [aqui](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP – 400 erros

### <a name="http-status-code"></a>Código de estado de HTTP
400

Existem diferentes razões pelas quais um pedido pode devolver 400. Abaixo estão alguns exemplos de erros devolvidos pela Azure Attestation APIs:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Falha no atestado devido a erros de avaliação de políticas

A política de atestado inclui regras de autorização e regras de emissão. As provas do enclave são avaliadas com base nas regras de autorização. As regras de emissão definem os pedidos a incluir no atestado token. Se as reclamações no enclave não cumprirem as regras de autorização, atestar chamadas devolva o erro de avaliação da política. 

**Código de erro** PolicyEvaluationError

**Exemplos de cenário** Quando as reclamações na cotação do enclave não correspondem às regras de autorização da política de atestado

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Etapas de resolução de problemas** Os utilizadores podem avaliar as provas do enclave contra uma política de atestado SGX antes de configurar o mesmo.

Envie um pedido para atestar a API fornecendo texto de política no parâmetro "draftPolicyForAttestation". A API AttestSgxEnclave utilizará este documento de política durante a chamada de atesta e este pode ser usado para testar políticas de atestado antes de serem consumidas. A ficha de atestado gerada quando este campo está presente não estará seguro.

Ver [exemplos de política de atestado](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Falha no atestado devido à entrada inválida

**Código de erro** InvalidParameter

**Exemplos de cenário** Falha no atestado SGX devido à entrada inválida. Abaixo estão alguns dos exemplos para mensagens de erro:
- A cotação especificada foi inválida devido a um erro na garantia de cotação 
- A cotação especificada foi inválida porque o dispositivo em que a cotação foi gerada não satisfaz os requisitos de base Azure
- A cotação especificada foi inválida porque o TCBInfo ou QEID fornecido pelo Serviço de Cache PCK foi inválido

**Passos de resolução de problemas**

O Microsoft Azure Attestation suporta a atestada de cotações SGX geradas pela Intel SDK e pela Open Enclave SDK.

Consulte [as amostras](/samples/browse/?expanded=azure&terms=attestation) de código para a realização de atestados utilizando o Open Enclave SDK/ Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Erro inválido da cadeia de certificados ao carregar a política/sinaleiro de política

**Código de erro** InvalidParameter

**Exemplos de cenário** Configure a política assinada ou o sinaleiro de política de adicionar/apagar, que é assinado com uma cadeia de certificados inválida (por exemplo, quando a extensão de Restrições Básicas do certificado raiz não é definida como Tipo Sujeito = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Etapas de resolução de problemas** O certificado de raiz deve ser sinalizado como sendo emitido por uma AC (os constrangimentos básicos X.509), caso contrário não será considerado como um certificado válido. 

Certifique-se de que a extensão das restrições básicas do certificado de raiz está definida para indicar que o Tipo de Sujeito = CA

Caso contrário, a cadeia de certificados é considerada inválida.

Consulte [o signatário da política](./policy-signer-examples.md) e os exemplos de [política](./policy-examples.md) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Adicionar/Eliminar falha do sinaleiro

**Código de erro** InválidaOperação

**Exemplos de cenário**

Quando o utilizador faz o upload do JWS sem a reivindicação "maa-policyCertificate"

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Quando o utilizador não faz o upload de um certificado em formato JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Etapas de resolução de problemas** Para adicionar/eliminar um novo certificado de sinalizador de política, utilize o RFC7519 JSON Web Token (JWT) com uma reivindicação denominada "x-ms-policyCertificate". O valor da reclamação é uma Chave Web RFC7517 JSON, que contém o certificado a ser adicionado. O JWT deve ser assinado com chave privada de qualquer um dos certificados de sinalização de política válidos associados ao fornecedor. Consulte [exemplos de signatários de políticas](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Falha na configuração da política de atestado

**Código de erro** PolicyParsingError

**Exemplos de cenário** Política fornecida com sintaxe incorreta (por exemplo, falta de semicolon)/política válida do JWT)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Código de erro** InválidaOperação

**Exemplos de cenário** Conteúdo inválido fornecido (por exemplo, política de upload/ política não assinada quando é necessária a assinatura de políticas)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Etapas de resolução de problemas** Certifique-se de que a política em formato texto está codificada pela UTF-8.

Se for necessária a assinatura de políticas, a política de atestação só deve ser configurada no formato RFC7519 JSON Web Token (JWT). Se a assinatura de política não for necessária, a política pode ser configurada em texto ou em formato JWT.

Para configurar uma política em formato JWT, utilize o JWT com uma reivindicação chamada "AttestationPolicy". O valor da reclamação é a versão codificada base64URL do texto de política. Se o prestador de atestado estiver configurado com certificados de sinalização de política, o JWT deve ser assinado com chave privada de qualquer um dos certificados de sinalização de política válidos associados ao fornecedor. 

Para configurar uma política em formato de texto, especifique o texto da política diretamente.

No PowerShell, especifique o PolicyFormat como JWT para configurar a política no formato JWT. O formato de política predefinido é Texto.

Veja [exemplos de política](./policy-examples.md) de atestado e [como autoriar uma política de atestado](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Problemas de instalação da Az.Attestation na PowerShell

Não é possível instalar módulos Az ou Az.Attestation no PowerShell

### <a name="error"></a>Erro

AVISO: Não foi possível resolver a fonte do pacote https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package : Não foi encontrada nenhuma correspondência para os critérios de pesquisa especificados e nome do módulo

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

A PowerShell Gallery depretou as versões 1.0 e 1.1 da Transport Layer Security (TLS). 

Recomenda-se tLS 1.2 ou uma versão posterior. 

Para continuar a interagir com a PowerShell Gallery, executar o seguinte comando antes que o Install-Module comandos

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Problemas de acesso/configuração de política no PowerShell

Utilizador designado com funções apropriadas. Mas enfrentar problemas de autorização enquanto gere as políticas de atestação através do PowerShell.

### <a name="error"></a>Erro
O cliente com identificação de objeto de objeto &lt; não tem autorização para executar &gt;  ação Microsoft.Autorização/roleassignments/write over scope 'subcrições/subscriçãoId &lt; &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/ &lt; role assignmentId ' ou o âmbito é &gt; inválido. Se o acesso foi concedido recentemente, por favor refresque as suas credenciais

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

A versão mínima dos módulos Az necessários para suportar as operações de atestado são as seguintes: 

 **Az 4.5.0** 
 
 **Az.Contas 1.9.2**
 
 **Az.Attestation 0.1.8** 

Executar o comando abaixo para verificar a versão instalada de todos os módulos Az 

```powershell
Get-InstalledModule 
```

Se as versões não corresponderem ao requisito mínimo, executar Update-Module comandos

por exemplo - Update-Module -Nome Az.Attestation
