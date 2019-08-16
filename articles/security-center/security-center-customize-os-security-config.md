---
title: Personalizar as configurações de segurança do sistema operacional na central de segurança do Azure (versão prévia) | Microsoft Docs
description: Este artigo demonstra como personalizar as avaliações da central de segurança
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
ms.date: 08/15/2019
ms.author: v-mohabe
ms.openlocfilehash: df4f51f97798048b28c0193dbc61e07fc55c9adc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535067"
---
# <a name="customize-os-security-configurations-in-azure-security-center-retired"></a>Personalizar as configurações de segurança do sistema operacional na central de segurança do Azure (desativado)

Este tutorial demonstra como personalizar as avaliações de configuração de segurança do sistema operacional (versão prévia) na central de segurança do Azure.

> [!NOTE]
> A capacidade de personalizar as configurações de segurança do sistema operacional (recurso de visualização) foi desativada em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_securityconfigurations).

## <a name="what-are-os-security-configurations"></a>O que são as configurações de segurança do so?

O Centro de Segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o SO, incluindo regras relacionadas com firewalls, auditoria, políticas de palavra-passe, entre outras. Se um computador apresentar uma configuração vulnerável, o Centro de Segurança gerará uma recomendação de segurança.

Personalizando as regras, as organizações podem controlar quais opções de configuração são mais apropriadas para seu ambiente. Você pode definir uma política de avaliação personalizada e, em seguida, aplicá-la em todos os computadores aplicáveis na assinatura.

> [!NOTE]
> - Atualmente, a personalização da configuração de segurança do sistema operacional está disponível somente para os sistemas operacionais Windows Server versões 2008, 2008 R2, 2012, 2012 R2 e 2016.
> - A configuração se aplica a todas as VMs e computadores que estão conectados a todos os espaços de trabalho na assinatura selecionada.
> - A personalização da configuração de segurança do so está disponível somente na camada Standard da central de segurança.
>
>

Você pode personalizar as regras de configuração de segurança do sistema operacional Habilitando e desabilitando uma regra específica, alterando a configuração desejada para uma regra existente ou adicionando uma nova regra com base nos tipos de regra com suporte (registro, política de auditoria e política de segurança). Atualmente, a configuração desejada deve ser um valor exato.

As novas regras devem estar no mesmo formato e estrutura que outras regras existentes do mesmo tipo.

> [!NOTE]
> Para personalizar as configurações de segurança do sistema operacional, você deve receber a função de *proprietário da assinatura*, *colaborador da assinatura*ou administrador de *segurança*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Personalizar a configuração de segurança padrão do sistema operacional

Para personalizar a configuração de segurança do sistema operacional padrão na central de segurança, faça o seguinte:

1.  Abra o dashboard **Centro de Segurança**.

2.  No painel esquerdo, selecione **preços & configurações**.

    ![Lista de políticas de segurança](media/security-center-customize-os-security-config/manual-provision.png)

4. Selecione a assinatura aplicável e selecione **Editar configurações de segurança**.  

    ![A janela "Editar configurações de segurança"](media/security-center-customize-os-security-config/blade.png)

5. Siga as etapas para baixar, editar e carregar o arquivo modificado.

   > [!NOTE]
   > Por padrão, o arquivo de configuração que você baixa está no formato *JSON* . Para obter instruções sobre como modificar esse arquivo, vá para [Personalizar o arquivo de configuração](#customize-the-configuration-file).
   >

6. Para confirmar a alteração, selecione **salvar**. Caso contrário, a política não será armazenada.

    ![O botão salvar](media/security-center-customize-os-security-config/save-successfully.png)

   Depois de salvar o arquivo com êxito, a configuração é aplicada a todas as VMs e computadores que estão conectados aos espaços de trabalho na assinatura. O processo geralmente leva alguns minutos, mas pode levar mais tempo, dependendo do tamanho da infraestrutura.

A qualquer momento, você pode redefinir a configuração da política atual para seu estado padrão. Para fazer isso, na janela **Editar regras de configuração de segurança do sistema operacional** , selecione **Redefinir**. Confirme essa opção selecionando **Sim** na janela pop-up de confirmação.

![A janela de confirmação de redefinição](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personalizar o arquivo de configuração

No arquivo de personalização, cada versão do sistema operacional com suporte tem um conjunto de regras ou RuleSet. Cada RuleSet tem seu próprio nome e ID exclusiva, conforme mostrado no exemplo a seguir:

![O nome e a ID do conjunto de regras](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Este arquivo de exemplo foi editado no Visual Studio, mas você também pode usar o bloco de notas se tiver instalado o plug-in do visualizador JSON.
>
>

Ao editar o arquivo de personalização, você pode modificar uma regra ou todas elas. Cada RuleSet inclui uma seção de *regras* que é separada em três categorias: Registro, política de auditoria e política de segurança, conforme mostrado aqui:

![Três categorias de RuleSet](media/security-center-customize-os-security-config/rules-section.png)

Cada categoria tem seu próprio conjunto de atributos. Você pode alterar os seguintes atributos:

- **expectedValue**: O tipo de dados do campo deste atributo deve corresponder aos valores com suporte por *tipo de regra*, por exemplo:

  - **baselineRegistryRules**: O valor deve corresponder ao [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) definido na regra.

  - **baselineAuditPolicyRules**: Use um dos seguintes valores de cadeia de caracteres:

    - *Êxito e falha*

    - *Êxito*

  - **baselineSecurityPolicyRules**: Use um dos seguintes valores de cadeia de caracteres:

    - *Não há um*

    - Lista de grupos de usuários permitidos, por exemplo: *Administradores*, *operadores de backup*

-   **estado**: A cadeia de caracteres pode conter as opções desabilitadas ou *habilitadas*. Para esta versão, a cadeia de caracteres diferencia maiúsculas de minúsculas.

Esses são os únicos campos que podem ser configurados. Se você violar o tamanho ou o formato do arquivo, não poderá salvar a alteração. Você receberá um erro informando que precisa carregar um arquivo de configuração JSON válido.

Para obter uma lista de outros erros potenciais, consulte [códigos de erro](#error-codes).

As três seções a seguir contêm exemplos das regras anteriores. Os atributos ExpectedValue e *State* podem ser alterados.

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

Algumas regras são duplicadas para os diferentes tipos de sistema operacional. As regras duplicadas têm o mesmo atributo OriginalID.

## <a name="create-custom-rules"></a>Criar regras personalizadas

Você também pode criar novas regras. Antes de criar uma nova regra, tenha em mente as seguintes restrições:

-   A versão do esquema, a baselineid e a *baselinename* não podem ser alteradas.

-   O conjunto de regras não pode ser removido.

-   O conjunto de regras não pode ser adicionado.

-   O número máximo de regras permitidas (incluindo as regras padrão) é 1000.

Novas regras personalizadas são marcadas com uma nova fonte personalizada (! = "Microsoft"). O campo RuleId pode ser nulo ou vazio. Se estiver vazio, a Microsoft gerará um. Se não estiver vazio, ele deverá ter um GUID válido que seja exclusivo em todas as regras (padrão e personalizada). Examine as seguintes restrições para os campos principais:

-   **originalId**: Pode ser nulo ou vazio. Se o OriginalID não estiver vazio, ele deverá ser um GUID válido.

-   **cceId**: Pode ser nulo ou vazio. Se *cceId* não estiver vazio, ele deverá ser exclusivo.

-   **RuleType**: (selecione um) registro, Auditpolicy ou SecurityPolicy.

-   **Severidade**: (selecione uma) desconhecido, crítico, aviso ou informativo.

-   **analyzeOperation**: Deve ser *igual a*.

-   **auditPolicyId**: Deve ser um GUID válido.

-   **regValueType**: (selecione um) int, Long, String ou multiplestring.

> [!NOTE]
> O hive deve ser *LocalMachine*.
>
>

Exemplo de uma nova regra personalizada:

**Registro**:
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

## <a name="file-upload-failures"></a>Falhas de upload de arquivo

Se o arquivo de configuração enviado for inválido devido a erros em valores ou formatação, um erro de falha será exibido, como a **ação salvar falha**. Você pode baixar um relatório detalhado de erros. csv para corrigir e consertar os erros antes de reenviar um arquivo de configuração corrigido.

Exemplo de um arquivo de erro:

![Exemplo de arquivo de erro](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Códigos de erro

Todos os erros potenciais estão listados na tabela a seguir:

| **Ao**                                | **Descrição**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | A propriedade *SchemaVersion* foi encontrada como inválida ou vazia. O valor deve ser definido como *{0}* .                                                         |
| BaselineInvalidStringError               | A propriedade *{0}* não pode conter  *\\n*.                                                                                                         |
| BaselineNullRuleError                    | A lista de regras de configuração de linha de base contém uma regra com o valor *NULL*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | O CCE-ID *{0}* não é exclusivo.                                                                                                                  |
| BaselineRuleEmptyProperty                | A propriedade *{0}* está ausente ou é inválida.                                                                                                       |
| BaselineRuleIdNotInDefault               | A regra tem uma propriedade de origem *Microsoft* , mas ela não foi encontrada no conjunto de regras padrão da Microsoft.                                                   |
| BaselineRuleIdNotUniqueError             | O identificador de regra não é exclusivo.                                                                                                                       |
| BaselineRuleInvalidGuid                  | A propriedade *{0}* foi considerada inválida. O valor não é um GUID válido.                                                                             |
| BaselineRuleInvalidHive                  | O hive deve ser LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | O nome da regra não é exclusivo.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | A regra não foi encontrada na nova configuração. A regra não pode ser excluída.                                                                     |
| BaselineRuleNotFoundError                | A regra não foi encontrada no conjunto de regras de linha de base padrão.                                                                                        |
| BaselineRuleNotInPlace                   | A regra corresponde a uma regra padrão com {0} o tipo e está {1} listada em lista.                                                                       |
| BaselineRulePropertyTooLong              | A propriedade *{0}* é muito longa. Comprimento máximo permitido: {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | O valor *{0}* esperado não corresponde ao tipo de valor do registro definido.                                                              |
| BaselineRulesetAdded                     | O conjunto de regras com *{0}* o identificador não foi encontrado na configuração padrão. O conjunto de regras não pode ser adicionado.                                               |
| BaselineRulesetIdMustBeUnique            | O RuleSet *{0}* de linha de base fornecido deve ser exclusivo.                                                                                           |
| BaselineRulesetNotFound                  | O conjunto de regras *{0}* com identificador *{1}* e nome não foi encontrado na configuração especificada. O conjunto de regras não pode ser excluído.                                |
| BaselineRuleSourceNotMatch               | A regra com o *{0}* identificador já está definida.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | O tipo de regra padrão *{0}* é.                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | O tipo real da regra é *{0}* , mas a propriedade *RuleType* é. *{1}*                                                                          |
| BaselineRuleUnpermittedChangesError      | Somente as propriedades ExpectedValue e *State* podem ser alteradas.                                                                       |
| BaselineTooManyRules                     | O número máximo de regras personalizadas permitidas {0} é regras. A configuração fornecida contém {1} regras, {2} regras padrão e {3} regras personalizadas. |
| ErrorNoConfigurationStatus               | Nenhum status de configuração foi encontrado. Declare o status da configuração desejada: *Padrão* ou *personalizado*.                                    |
| ErrorNonEmptyRulesetOnDefault            | O estado de configuração é definido como padrão. A lista *BaselineRulesets* deve ser nula ou vazia.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | O status de configuração fornecido é *personalizado* , mas a propriedade *baselineRulesets* é nula ou está vazia.                                             |
| ErrorParsingBaselineConfig               | A configuração fornecida é inválida. Um ou mais valores definidos têm um valor nulo ou um tipo inválido.                                  |
| ErrorParsingIsDefaultProperty            | O valor *{0}* de configurationStatus fornecido é inválido. O valor só pode ser *padrão* ou *personalizado*.                                         |
| InCompatibleViewVersion                  | Não há suporte *{0}* para a versão de exibição neste tipo de espaço de trabalho.                                                                                   |
| InvalidBaselineConfigurationGeneralError | A configuração de linha de base fornecida foi encontrada com um ou mais erros de validação de tipo.                                                          |
| ViewConversionError                      | A exibição é uma versão mais antiga do que o espaço de trabalho dá suporte. Falha na conversão da {0}exibição:.                                                                 |

Se você não tiver permissões suficientes, poderá obter um erro geral de falha, como mostrado aqui:

![Mensagem de erro "falha ao salvar ação"](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Passos seguintes
Este artigo abordou como personalizar as avaliações de configuração de segurança do sistema operacional na central de segurança. Para saber mais sobre as regras de configuração e a correção, consulte:

- [Identificadores de configuração comuns da central de segurança e regras de linha de base](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A central de segurança usa a CCE (Enumeração de configuração comum) para atribuir identificadores exclusivos às regras de configuração. Para obter mais informações, consulte [CCE](https://nvd.nist.gov/config/cce/index).
- Para resolver vulnerabilidades quando a configuração do sistema operacional não corresponde às regras de configuração de segurança recomendadas, consulte [corrigir as configurações de segurança](security-center-virtual-machine-protection.md).
