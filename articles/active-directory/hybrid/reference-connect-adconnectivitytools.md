---
title: 'Azure AD Connect: ADConnectivityTools PowerShell Reference [ Microsoft Docs'
description: Este documento fornece informações de referência para o módulo ADConnectivityTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66473798"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Ligação Azure AD: Referência aDConnectivityTools PowerShell

A seguinte documentação fornece informações de referência para o Módulo PowerShell ADConnectivityTools.psm1 que está incluído com Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirmar-DnsConectividade

### <a name="synopsis"></a>SINOPSE

Deteta problemas locais de Dns.

### <a name="syntax"></a>SINTAXE

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Executa testes locais de conectividade Dns.
Para configurar o conector Ative Directory, o utilizador deve ter ambas as resoluções de nome solução para a floresta a que estão a tentar ligar-se, bem como nos controladores de domínio associados a esta floresta.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Especifica o nome da floresta para testar.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-CDs

Especifique os DCs para testar contra.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Devolve o resultado deste diagnóstico sob a forma de um PSObject.
Não é necessário durante a interação manual com esta ferramenta.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-forestexists"></a>Confirm-ForestExist

### <a name="synopsis"></a>SINOPSE

Determina se existe uma floresta especificada.

### <a name="syntax"></a>SINTAXE

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Consulta um servidor DNS para os endereços IP associados a uma floresta.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Especifica o nome da floresta para testar.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-functionallevel"></a>Nível de confirmação-funcional

### <a name="synopsis"></a>SINOPSE

Verifica o nível funcional da floresta aD.

### <a name="syntax"></a>SINTAXE

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFqdn

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Verifica que o nível funcional da floresta AD é igual ou mais do que um dado MinAdForestVersion (WindowsServer2003).
Conta (Nome de utilizador de domínio) e palavra-passe podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPLO 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Floresta alvo.
O valor predefinido é a Floresta do atualmente registado no utilizador.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFqdN

Alvo Objeto Forestfqdn.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A função utilizará as credenciais do utilizador que está atualmente registada no computador, em vez de solicitar credenciais personalizadas ao utilizador.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SINOPSE

Deteta problemas de conectividade de rede local.

### <a name="syntax"></a>SINTAXE

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Executa testes de conectividade de rede local.

Para os testes de networking locais, o AAD Connect deve ser capaz de comunicar com os controladores de domínio nomeados nas portas 53 (DNS), 88 (Kerberos) e 389 (LDAP) A maioria das organizações executa DNS nos seus DCs, razão pela qual este teste está atualmente integrado.
A porta 53 deve ser ignorada se outro servidor DNS tiver sido especificado.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-dcs"></a>-CDs

Especifique os DCs para testar contra.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Se o utilizador não estiver a utilizar os serviços DNS fornecidos pelo Site AD /Logon DC, então poderá querer saltar a porta de verificação 53.
O utilizador deve ainda ser capaz de resolver _.ldap._tcp. \<forestfqdn\> para que a configuração do Conector de Diretório Ativo tenha sucesso.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Devolve o resultado deste diagnóstico sob a forma de um PSObject.
Não é necessário durante a interação manual com esta ferramenta.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>SINOPSE

Determina se uma floresta especificada e os seus controladores de domínio associados são acessíveis.

### <a name="syntax"></a>SINTAXE

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Executa testes "ping" (se um computador pode chegar a um computador de destino através da rede e/ou da internet)

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Especifica o nome da floresta para testar.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-CDs

Especifique os DCs para testar contra.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-validdomains"></a>Confirmar-ValidDomínios

### <a name="synopsis"></a>SINOPSE

Valide que os domínios do FQDN florestal obtido são acessíveis

### <a name="syntax"></a>SINTAXE

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFqdn

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Valide que todos os domínios da Floresta FQDN obtida são acessíveis tentando recuperar DomainGuid e DomainDN.
Conta (Nome de utilizador de domínio) e palavra-passe podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>EXEMPLO 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Floresta alvo.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFqdN

Alvo Objeto Forestfqdn.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A função utilizará as credenciais do utilizador que está atualmente registada no computador, em vez de solicitar credenciais personalizadas ao utilizador.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SINOPSE

Verifica se um utilizador tem credenciais de administrador da Enterprise.

### <a name="syntax"></a>SINTAXE

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Pesquisas se fornecido utilizador tem credenciais de Administração Da Empresa.
Conta (Nome de utilizador de domínio) e palavra-passe podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A função utilizará as credenciais do utilizador que está atualmente registada no computador, em vez de solicitar credenciais personalizadas ao utilizador.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SINOPSE

Recupera um DomainFQDN de uma combinação de conta e palavra-passe.

### <a name="syntax"></a>SINTAXE

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Tenta obter um objeto de domínioFQDN a partir de credenciais fornecidas.
Se o domínioFQDN for válido, será devolvido um Nome de DomínioFQDNname ou RootDomain, dependendo da escolha do utilizador.
Conta (Nome de utilizador de domínio) e palavra-passe podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Tipo de dados desejados que serão recuperados.
Atualmente limitado a "DomainFQDNName" ou "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A função utilizará as credenciais do utilizador que está atualmente registada no computador, em vez de solicitar credenciais personalizadas ao utilizador.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionError

Parâmetro auxiliar utilizado pela função Start-NetworkConnectivityDiagnosticsTools

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="get-forestfqdn"></a>Get-ForestFqdN

### <a name="synopsis"></a>SINOPSE

Recupera um ForestFQDN de uma combinação de conta e palavra-passe.

### <a name="syntax"></a>SINTAXE

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Tenta obter um ForestFQDN das credenciais fornecidas.
Conta (Nome de utilizador de domínio) e palavra-passe podem ser solicitadas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Floresta alvo. O valor predefinido é o Domínio do atualmente registado no utilizador.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

A função utilizará as credenciais do utilizador que está atualmente registada no computador, em vez de solicitar credenciais personalizadas ao utilizador.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="start-connectivityvalidation"></a>Validação de início de conectividade

### <a name="synopsis"></a>SINOPSE

Função principal.

### <a name="syntax"></a>SINTAXE

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Executa todos os mecanismos disponíveis que verifiquem que as credenciais ad são válidas.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Floresta alvo.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Para instalações personalizadas: A bandeira que é $True se o utilizador escolher "Criar uma nova conta AD" na janela da Conta Florestal AD do assistente do AADConnect.
$False se o utilizador escolher "Use a conta AD existente".
Para instalações expressas: O valor desta variável deve ser $True para as instalações expressas.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parâmetro que pré-povoa o campo username quando as credenciais do utilizador são solicitadas.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .

## <a name="start-networkconnectivitydiagnosistools"></a>Ferramentas de Diagnóstico de Conectividade inicial

### <a name="synopsis"></a>SINOPSE

Função principal para testes de conectividade de rede.

### <a name="syntax"></a>SINTAXE

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIÇÃO

Executa testes de conectividade de rede local.

### <a name="examples"></a>EXEMPLOS

#### <a name="example-1"></a>EXEMPLO 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>EXEMPLO 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARÂMETROS

#### <a name="-forest"></a>-Floresta

Especifica o nome da floresta para testar.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Credenciais

O nome de utilizador e a palavra-passe do utilizador que está a executar o teste.
Requer o mesmo nível de permissões que é necessário para executar o Assistente de Ligação AD Azure.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Especifica a localização de um ficheiro de registo que conterá a saída desta função.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-CDs

Especifique os DCs para testar contra.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayMensagem Informativa

Bandeira que permite exibir uma mensagem sobre o propósito desta função.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Devolve o resultado deste diagnóstico sob a forma de um PSObject.
Não é necessário especificar durante a interação manual com esta ferramenta.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-Credenciais válidas

Indica se as credenciais que o utilizador digitou são válidas.
Não é necessário especificar durante a interação manual com esta ferramenta.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Este cmdlet suporta os parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Para mais informações,https://go.microsoft.com/fwlink/?LinkID=113216)consulte about_CommonParameters .
