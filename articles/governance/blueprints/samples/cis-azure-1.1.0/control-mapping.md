---
title: Exemplo-CIS Microsoft Azure fundamentos de benchmark – mapeamento de recomendação
description: Mapeamento de recomendação do exemplo de plano de referência do CIS Microsoft Azure Foundations para Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f048262003a3567175c40ebf4ee744c41e11b5f9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918699"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapeamento de recomendação da amostra Blueprint de benchmark do Microsoft Azure Foundations do CIS

O artigo a seguir fornece detalhes sobre como a amostra do plano gráfico de benchmark do Azure Microsoft Azure Foundations do CIS é mapeada para as recomendações de benchmark Microsoft Azure Foundations. Para obter mais informações sobre as recomendações, consulte [CIS Microsoft Azure Foundations benchmark](https://www.cisecurity.org/benchmark/azure/).

Os mapeamentos a seguir são para as recomendações de **benchmark v 1.1.0 do Microsoft Azure Foundations do CIS** . Use a navegação à direita para ir diretamente para um mapeamento de recomendação específico.
Muitas das recomendações mapeadas são implementadas com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione as recomendações de auditoria de versão  **\[prévia\] do CIS Microsoft Azure Foundations benchmark v 1.1.0 e implante extensões de VM específicas para dar suporte** à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 garantir que a autenticação multifator esteja habilitada para todos os usuários privilegiados

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que se alinham com esta recomendação de CIS.

- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada em contas com permissões de gravação em sua assinatura

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 garantir que a autenticação multifator esteja habilitada para todos os usuários sem privilégios

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Verifique se não há usuários convidados

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que se alinham com esta recomendação de CIS.

- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de leitura devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Verifique se a configuração de política padrão ASC "monitorar atualizações do sistema" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As atualizações do sistema devem ser instaladas em seus computadores

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Verifique se a configuração de política padrão ASC "monitorar vulnerabilidades do sistema operacional" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Verifique se a configuração de política padrão ASC "monitor Endpoint Protection" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Verifique se a configuração de política padrão ASC "monitorar criptografia de disco" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Verifique se a configuração de política padrão ASC "monitorar o Firewall do aplicativo Web" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As regras de NSGs para aplicativos Web em IaaS devem ser protegidas

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 Verifique se a configuração de política padrão ASC "monitorar avaliação de vulnerabilidade" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Verifique se a configuração de política padrão ASC "monitorar acesso à rede JIT" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 Verifique se a configuração de política padrão ASC "monitorar criptografia de SQL" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Verifique se ' transferência segura necessária ' está definido como ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A transferência segura para contas de armazenamento deve ser habilitada

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Verifique se a regra de acesso de rede padrão para contas de armazenamento está definida como negar

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Verifique se a ' auditoria ' está definida como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Verifique se "AuditActionGroups" na política de "auditoria" de um SQL Server está definido corretamente

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As configurações de auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 garantir que a retenção de ' auditoria ' seja ' maior que 90 dias '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Os SQL Servers devem ser configurados com dias de retenção de auditoria maiores que 90 dias.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Verifique se a ' segurança de dados avançada ' em um SQL Server está definida como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A segurança de dados avançada deve estar ativada nos seus servidores SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Verifique se ' tipos de detecção de ameaças ' está definido como ' todos '

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que se alinham com esta recomendação de CIS.

- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' nas configurações de segurança de dados avançadas do SQL Server
- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' na instância gerenciada do SQL configurações de segurança de dados avançadas

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Verifique se ' enviar alertas para ' está definido

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As configurações avançadas de segurança de dados para o SQL Server devem conter um endereço de email para receber alertas de segurança

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 Verifique se ' serviço de email e coadministradores ' está ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As configurações de segurança de dados avançadas para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 Verifique se o administrador do Azure Active Directory está configurado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Verifique se a ' criptografia de dados ' está definida como ' on ' em um banco de dados SQL

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 garantir que o protetor de TDE do SQL Server seja criptografado com BYOK (Use sua própria chave)

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que se alinham com esta recomendação de CIS.

- O protetor de TDE do servidor SQL deve estar encriptado com a sua própria chave
- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 garantir que o log do Azure keyvault esteja ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Os logs de diagnóstico no Key Vault devem ser habilitados

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 garantir que o ' disco do sistema operacional ' esteja criptografado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 garantir que os ' discos de dados ' estejam criptografados

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 garantir que os patches do sistema operacional mais recentes para todas as máquinas virtuais sejam aplicados

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- As atualizações do sistema devem ser instaladas em seus computadores

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 garantir que o Endpoint Protection para todas as máquinas virtuais esteja instalado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 habilitar o RBAC (controle de acesso baseado em função) nos serviços Kubernetess do Azure

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- \[Versão\]prévia: O RBAC (controle de acesso baseado em função) deve ser usado nos serviços Kubernetess

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 garantir que o aplicativo Web Redirecione todo o tráfego HTTP para HTTPS no serviço Azure App

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que se alinha com essa recomendação de CIS.

- Aplicação Web só deve estar acessível através de HTTPS

## <a name="next-steps"></a>Passos Seguintes

Agora que você examinou o mapeamento de controle do plano de referência do CIS Microsoft Azure Foundations, visite o seguinte artigo para saber mais sobre o plano gráfico ou visite Azure Policy no portal do Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [Plano de referência do CIS Microsoft Azure Foundations Blueprint-visão geral](./index.md)
> [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).