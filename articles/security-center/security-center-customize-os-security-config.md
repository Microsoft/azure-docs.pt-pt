---
title: Personalizar as configurações de segurança de SO no Centro de segurança do Azure (pré-visualização) | Documentos da Microsoft
description: Este artigo demonstra como personalizar as avaliações de centro de segurança
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c0c37724e61490c8c33b5e2d37879549bbc6d7ce
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402121"
---
# <a name="customize-os-security-configurations-in-azure-security-center-preview"></a>Personalizar as configurações de segurança de SO no Centro de segurança do Azure (pré-visualização)

Estas instruções demonstram como personalizar as avaliações de configuração de segurança de SO no Centro de segurança do Azure.

## <a name="what-are-os-security-configurations"></a>Quais são as configurações de segurança do sistema operacional?

Centro de segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 recomendado regras](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o sistema operacional, incluindo regras relacionadas com firewalls, auditoria, as políticas de palavra-passe e muito mais. Se uma máquina é encontrada ter uma configuração vulnerável, o Centro de segurança gera uma recomendação de segurança.

Ao personalizar as regras, as organizações podem controlar quais opções de configuração são mais adequadas para o seu ambiente. Pode definir uma política personalizada de avaliação e, em seguida, aplicá-la em todos os computadores aplicáveis na subscrição.

> [!NOTE]
> - Atualmente, a personalização da configuração de segurança do sistema operacional está disponível para Windows Server 2008, 2008 R2, 2012, 2012 R2 e 2016 de versões apenas nos sistemas operativos.
> - A configuração é aplicada a todas as VMs e computadores que estejam ligados a todas as áreas de trabalho sob a subscrição selecionada.
> - Personalização de configuração de segurança do SO está disponível apenas no escalão standard do Centro de segurança.
>
>

Pode personalizar as regras de configuração de segurança do sistema operacional ao ativar e desativar uma regra específica, alterar a definição pretendida para uma regra existente ou adicionar uma nova regra que baseia-se sobre os tipos de regra suportados (registo, política de auditoria e a política de segurança). Atualmente, a definição pretendida tem de ser um valor exato.

Novas regras tem de ser o mesmo formato e estrutura como outras regras existentes do mesmo tipo.

> [!NOTE]
> Para personalizar as configurações de segurança do SO, tem de ser atribuída a função de *proprietário da subscrição*, *contribuinte da subscrição*, ou *administrador de segurança*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Personalizar a configuração de segurança do sistema operacional padrão

Para personalizar a configuração de segurança do sistema operacional padrão no Centro de segurança, faça o seguinte:

1.  Abra o dashboard **Centro de Segurança**.

2.  No painel esquerdo, selecione **política de segurança**.      

    ![Lista de políticas de segurança](media/security-center-customize-os-security-config/manual-provision.png)

3.  Na linha da subscrição que pretende personalizar, clique em **editar as definições de**.

4. Selecione **editar configurações de segurança**.  

    ![A janela de "Editar configurações de segurança"](media/security-center-customize-os-security-config/blade.png)

5. Siga os passos para transferir, editar e carregar o arquivo modificado.

   > [!NOTE]
   > Por predefinição, o ficheiro de configuração que baixar está no *json* formato. Para obter instruções sobre como modificar esse arquivo, aceda a [personalizar o ficheiro de configuração](#customize-the-configuration-file).
   >

6. Para consolidar a alteração, selecione **guardar**. Caso contrário, a política não é armazenada.

    ![O botão salvar](media/security-center-customize-os-security-config/save-successfully.png)

   Depois de ter guardado com êxito o ficheiro, a configuração é aplicada a todas as VMs e computadores que estão ligados a áreas de trabalho sob a subscrição. O processo normalmente demora alguns minutos mas pode demorar mais tempo, dependendo do tamanho de infraestrutura.

Em qualquer momento, pode repor a configuração da política atual no estado predefinido. Para tal, na **regras de configuração de segurança de editar o sistema operacional** janela, selecione **repor**. Confirme esta opção, selecionando **Sim** na janela de pop-up de confirmação.

![A janela de confirmação de reposição](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalizar o ficheiro de configuração

O arquivo de personalização, cada versão suportada do SO tem um conjunto de regras ou conjunto de regras. Cada conjunto de regras tem seu próprio nome e o ID exclusivo, conforme mostrado no exemplo a seguir:

![O nome do conjunto de regras e ID](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Este ficheiro de exemplo foi editado no Visual Studio, mas também pode utilizar o bloco de notas se tiver o plug-in de Visualizador de JSON instalado.
>
>

Ao editar o arquivo de personalização, pode modificar uma regra ou todos eles. Cada conjunto de regras inclui um *regras* secção é separada em três categorias: Registo, a política de auditoria e a política de segurança, como mostrado aqui:

![Três categorias de conjunto de regras](media/security-center-customize-os-security-config/rules-section.png)

Cada categoria tem seu próprio conjunto de atributos. Pode alterar os seguintes atributos:

- **expectedValue**: Tipo de dados do campo este atributo tem de corresponder aos valores suportados por *tipo de regra*, por exemplo:

  - **baselineRegistryRules**: O valor deve corresponder a [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) que é definida nessa regra.

  - **baselineAuditPolicyRules**: Utilize um dos seguintes valores de cadeia de caracteres:

    - *Êxito e falha*

    - *Êxito*

  - **baselineSecurityPolicyRules**: Utilize um dos seguintes valores de cadeia de caracteres:

    - *Ninguém*

    - Lista de grupos de utilizadores permitidos, por exemplo: *Os administradores*, *operadores de cópia de segurança*

-   **state**: A cadeia de caracteres pode conter as opções *desativada* ou *ativado*. Nesta versão, a cadeia de caracteres diferencia maiúsculas de minúsculas.

Estes são os campos únicos que podem ser configurados. Se violar o formato de ficheiro ou o tamanho, não será possível guardar a alteração. Receberá uma mensagem de erro informando que terá de carregar um ficheiro de configuração JSON válido.

Para obter uma lista de outros erros possíveis, consulte [códigos de erro](#error-codes).

As três seções seguintes contêm exemplos das regras anteriores. O *expectedValue* e *estado* atributos podem ser alterados.

**baselineRegistryRules**
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```json
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```json
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Algumas regras são duplicadas para os diferentes tipos de SO. Regras duplicadas têm a mesma *originalId* atributo.

## <a name="create-custom-rules"></a>Criar regras personalizadas

Também pode criar novas regras. Antes de criar uma nova regra, tenha em atenção as seguintes restrições:

-   Versão de esquema *baselineId* e *baselineName* não pode ser alterado.

-   Não é possível remover o conjunto de regras.

-   Não é possível adicionar o conjunto de regras.

-   O número máximo de regras de permissão (incluindo as regras predefinidas) é 1000.

Novas regras personalizadas são marcadas com uma nova origem personalizada (! = "Microsoft"). O *ruleId* campo pode ser nulo nem estar vazio. Se estiver vazia, a Microsoft gera um. Se não estiver vazia, tem de ter um GUID válido que seja exclusivo em todas as regras (padrão e personalizada). Reveja as seguintes restrições para os campos de núcleos:

-   **originalId**: Pode ser nulo nem estar vazio. Se *originalId* é não vazio, deve ser um GUID válido.

-   **cceId**: Pode ser nulo nem estar vazio. Se *cceId* é não vazio, tem de ser exclusivo.

-   **ruleType**: (selecione uma) registro, AuditPolicy ou SecurityPolicy.

-   **Gravidade**: (selecione uma) desconhecido, crítico, aviso ou informativo.

-   **analyzeOperation**: Tem de ser *é igual a*.

-   **auditPolicyId**: Tem de ser um GUID válido.

-   **regValueType**: (selecione uma) Int, Long, String, ou MultipleString.

> [!NOTE]
> Ramo de registo tem de ser *LocalMachine*.
>
>

Exemplo de uma nova regra personalizada:

**Registo**:
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Política de segurança**:
```json
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Política de auditoria**:
```json
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Falhas de carregamento de ficheiros

Se o ficheiro de configuração submetido é inválido devido a erros no valores ou a formatação, um erro de falha é apresentado, tais como **ação de guardar falhou**. Pode transferir um relatório do. csv de erros detalhados para remediar e corrigir os erros antes de voltar a submeter um ficheiro de configuração corrigido.

Exemplo de um ficheiro de erro:

![Exemplo de ficheiro de erro](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Códigos de erro

Todos os erros potenciais estão listados na tabela a seguir:

| **Error**                                | **Descrição**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | A propriedade *schemaVersion* foi encontrado inválido ou está vazio. O valor tem de ser definido como *{0}*.                                                         |
| BaselineInvalidStringError               | A propriedade *{0}* não pode conter  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | A lista de regras de configuração de linha de base contém uma regra com o valor *nulo*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | O ID do CCE *{0}* não é exclusivo.                                                                                                                  |
| BaselineRuleEmptyProperty                | A propriedade *{0}* está em falta ou é inválido.                                                                                                       |
| BaselineRuleIdNotInDefault               | A regra tem uma propriedade de origem *Microsoft* mas não foi encontrada no conjunto de regras de predefinição da Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | O ID da regra não é exclusivo.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A propriedade *{0}* foi encontrado inválido. O valor não é um GUID válido.                                                                             |
| BaselineRuleInvalidHive                  | O ramo de registo tem de ser LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | O nome da regra não é exclusivo.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A regra não foi encontrada na configuração de novo. Não é possível eliminar a regra.                                                                     |
| BaselineRuleNotFoundError                | A regra não foi encontrada em default conjunto de regras de linha de base.                                                                                        |
| BaselineRuleNotInPlace                   | A regra corresponder uma regra predefinida com o tipo {0} e está listado no {1} lista.                                                                       |
| BaselineRulePropertyTooLong              | A propriedade *{0}* é demasiado longo. Máximo permitido de comprimento: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | O valor esperado *{0}* não corresponde ao tipo de valor de registo que está definido.                                                              |
| BaselineRulesetAdded                     | O conjunto de regras com o ID *{0}* não foi encontrado na configuração predefinida. Não é possível adicionar o conjunto de regras.                                               |
| BaselineRulesetIdMustBeUnique            | O conjunto de regras de linha de base especificada *{0}* tem de ser exclusivo.                                                                                           |
| BaselineRulesetNotFound                  | O conjunto de regras com o id *{0}* e o nome *{1}* não foi encontrado na configuração especificada. Não é possível eliminar o conjunto de regras.                                |
| BaselineRuleSourceNotMatch               | A regra com ID *{0}* já está definido.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | É o tipo de regra predefinida *{0}*.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | É o tipo real da regra *{0}*, mas o *ruleType* propriedade é *{1}*.                                                                          |
| BaselineRuleUnpermittedChangesError      | Apenas *expectedValue* e *estado* propriedades têm permissão para ser alterada.                                                                       |
| BaselineTooManyRules                     | O número máximo de regras personalizadas permitidas é {0} regras. Contém a configuração de determinado {1} regras, {2} regras, predefinidas e {3} personalizado regras. |
| ErrorNoConfigurationStatus               | Não foi encontrado nenhum Estado de configuração. Estado do Estado de configuração pretendida: *Predefinido* ou *personalizado*.                                    |
| ErrorNonEmptyRulesetOnDefault            | O estado da configuração é definido como predefinido. O *BaselineRulesets* lista tem de ser nulo nem estar vazio.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | O estado de determinada configuração é *personalizada* , mas a *baselineRulesets* propriedade é nulo ou estar vazio.                                             |
| ErrorParsingBaselineConfig               | A configuração especificada é inválida. Um ou mais dos valores definidos tem um valor nulo ou um tipo inválido.                                  |
| ErrorParsingIsDefaultProperty            | O determinado *configurationStatus* valor *{0}* é inválido. O valor só pode ser *predefinido* ou *personalizado*.                                         |
| InCompatibleViewVersion                  | A versão do modo de exibição *{0}* é *não* suportado neste tipo de área de trabalho.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A configuração de linha de base especificada foi encontrada com um ou mais erros de validação de tipo.                                                          |
| ViewConversionError                      | A visualização é uma versão mais antiga que oferece suporte a área de trabalho. Falha na conversão de ver: {0}.                                                                 |

Se não tiver permissões suficientes, poderá obter um erro de falha geral, como mostrado aqui:

![Mensagem de erro "salvar a ação falhou"](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Passos Seguintes
Este artigo abordou a forma de personalizar as avaliações de configuração de segurança de SO no Centro de segurança. Para saber mais sobre as regras de configuração e a remediação, consulte:

- [Identificadores de configuração do Centro de segurança comuns e regras de linha de base](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Centro de segurança utiliza a enumeração de configuração comum (CCE) para atribuir os identificadores exclusivos para as regras de configuração. Para obter mais informações, consulte [CCE](https://nvd.nist.gov/config/cce/index).
- Para resolver vulnerabilidades quando a configuração do SO não coincide com as regras de configuração de segurança recomendado, veja [remediar configurações de segurança](security-center-remediate-os-vulnerabilities.md).
