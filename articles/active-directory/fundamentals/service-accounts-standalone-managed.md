---
title: Garantir contas de serviço geridas autónomas | Diretório Ativo Azure
description: Um guia para garantir contas de serviço geridas autónomas.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548857"
---
# <a name="securing-standalone-managed-service-accounts"></a>Assegurar contas de serviço geridas autónomas

As Contas de Serviço Gerido Independente (SMSAs) são contas de domínio geridas utilizadas para garantir um ou mais serviços em execução num servidor. Não podem ser reutilizados em vários servidores. os sMSAs fornecem gestão automática de passwords, gestão simplificada do nome principal do serviço (SPN) e a capacidade de delegar a gestão a outros administradores. 

No Ative Directory, as SMSAs estão ligadas a um servidor específico que executa um serviço. Pode encontrar estas contas listadas no snap-in de Utilizadores e Computadores de Ative Directory da Consola de Gestão da Microsoft.

![Uma imagem de ecrã dos utilizadores e computadores do Ative Directory mostrando as contas de serviço geridas OU.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

As Contas de Serviço Gerido foram introduzidas com o Windows Server 2008R2 Ative Directory Schema e requerem um nível de SISTEMA mínimo de Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Benefícios da utilização de SMSAs

as SMSAs oferecem maior segurança do que as contas de utilizador utilizadas como contas de serviço, reduzindo ao mesmo tempo as despesas administrativas através de:

* Definindo senhas fortes. as sMSAs usam 240 bytes gerados aleatoriamente senhas complexas geradas. A complexidade e o comprimento das palavras-passe sMSA minimizam a probabilidade de um serviço ser comprometido por força bruta ou ataques de dicionário.

* Senhas de ciclismo regularmente. O Windows altera automaticamente a palavra-passe sMSA a cada 30 dias. Os administradores de serviço e de domínio não precisam de agendar alterações de palavras-passe ou de gerir o tempo de inatividade associado.

* Simplificação da gestão da SPN. Os nomes principais do serviço são automaticamente atualizados se o Nível Funcional do Domínio (DFL) for o Windows Server 2008 R2. Por exemplo, o nome principal do serviço é atualizado automaticamente nos seguintes cenários:

   * A conta do computador anfitrião é renomeada. 

   * O nome DNS do computador anfitrião é alterado.

   * Ao adicionar ou remover um nome adicional de conta de sam ou parâmetros dns-hostname usando [PowerShell](/powershell/module/addsadministration/set-adserviceaccount)

## <a name="when-to-use-smsas"></a>Quando usar sMSAs

sMSAs podem simplificar tarefas de gestão e segurança. Utilize sMSAs quando tiver um ou mais serviços implantados num único servidor, e não pode utilizar um gMSA. 

> [!NOTE] 
> Embora possa utilizar sMSAs para mais de um serviço, recomendamos que cada serviço tenha a sua própria identidade para fins de auditoria. 

Se o criador do software não lhe puder dizer se pode utilizar um MSA, tem de testar a sua aplicação. Para tal, crie um ambiente de teste e garanta que pode aceder a todos os recursos necessários. Consulte [criar e instalar um SMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) para as instruções passo a passo.

### <a name="assess-security-posture-of-smsas"></a>Avaliar a postura de segurança das SMSAs

as sMSAs são inerentemente mais seguras do que as contas padrão do utilizador, que requerem uma gestão contínua da palavra-passe. No entanto, é importante considerar o âmbito de acesso das SMSAs como parte da sua postura de segurança geral.

A tabela a seguir mostra como atenuar potenciais problemas de segurança colocados pelas SMSAs.

| Problemas de segurança| Mitigações |
| - | - |
| sMSA é um membro de grupos privilegiados|Remova o sMSA de grupos privilegiados elevados (tais como Administrações de Domínio). <br> Utilize o modelo menos privilegiado e conceda à SMSA apenas os direitos e permissões que necessita para executar os seus serviços. <br> Se não tiver a certeza das permissões necessárias, consulte o criador do serviço. |
| sMSA tem acesso de leitura/escrita a recursos sensíveis.|Auditoria acesso a recursos sensíveis. Arquivar registos de auditoria a um SIEM (Azure Log Analytics ou Azure Sentinel) para análise. <br> Remediar permissões de recursos se for detetado um nível de acesso indesejável. |
| Por predefinição, a frequência de capotamento de password sMSA é de 30 dias| A política de grupo pode ser usada para afinar a duração dependendo dos requisitos de segurança da empresa. <br> *Pode definir a duração de validade da palavra-passe utilizando o seguinte caminho. <br>Configuração do computador\Políticas\Definições do Windows\Definições de segurança\Opções de Segurança\Membro do domínio: Idade máxima da palavra-passe da conta da máquina |



### <a name="challenges-with-smsas"></a>Desafios com sMSAs

Os desafios associados às PME são os seguintes:

| Desafios| Mitigações |
| - | - |
| Podem ser usados num único servidor.| Utilize gMSAs se precisar de utilizar a conta através dos servidores. |
| Não podem ser usados em domínios.| Utilize gMSAs se precisar de utilizar a conta através de domínios. |
| Nem todas as aplicações suportam sMSAs.| Utilize gMSAs se possível. Se não utilizar uma conta de utilizador padrão ou uma conta de computador como recomendado pelo criador da aplicação. |


## <a name="find-smsas"></a>Encontrar sMSAs

Em qualquer controlador de domínio, executar DSA.msc e expandir o contentor Contas de Serviço Gerido para visualizar todas as SMSAs. 

O seguinte comando PowerShell devolve todas as SMSAs e gMSAs no domínio do Diretório Ativo. 

`Get-ADServiceAccount -Filter *`

O comando que se segue retorna apenas as SMSAs no domínio do Diretório Ativo.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Gerir sMSAs

Pode utilizar os seguintes cmdlets Ative Directory PowerShell para gerir as SMSAs:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Mude-se para as SMSAs

Se um serviço de aplicação suporta sMSA mas não gMSAs, e está atualmente a utilizar uma conta de utilizador ou conta de computador para o contexto de segurança, [crie e instale um SMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) no servidor. 

Idealmente, mova recursos para Azure, e use identidades geridas azure ou diretores de serviço.

 

## <a name="next-steps"></a>Passos seguintes
Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)

