---
title: Introdução às contas de serviço de diretório ativo | Diretório Ativo Azure
description: Uma introdução aos tipos de contas de serviço no Ative Directory e como as assegurar.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645621"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Introdução às contas de serviço do Ative Directory

Um serviço tem uma identidade de segurança primária que determina os direitos de acesso dos recursos locais e de rede. O contexto de segurança de um serviço Microsoft Win32 é determinado pela conta de serviço que é utilizada para iniciar o serviço. Uma conta de serviço é usada para:
* identificar e autenticar um serviço
* iniciar com sucesso um serviço
* aceder ou executar código ou uma aplicação
* iniciar um processo. 

## <a name="types-of-on-premises-service-accounts"></a>Tipos de contas de serviço no local

Com base no seu caso de utilização, pode utilizar uma conta de serviço gerida (MSA), uma conta de computador ou uma conta de utilizador para executar um serviço. Os serviços devem ser testados para confirmar que podem utilizar uma conta de serviço gerida. Se puderem, devias usar um.

### <a name="group-msa-accounts"></a>Contas MSA do grupo

Utilize [contas de serviço geridas pelo grupo](service-accounts-group-managed.md) (gMSAs) sempre que possível para serviços que executam no seu ambiente no local. gMSAs fornecem uma única solução de identidade para um serviço que funciona numa fazenda de servidor, ou atrás de um equilibrador de carga de rede. Também podem ser utilizados para um serviço em execução num único servidor. [gMSAs têm requisitos específicos que devem ser cumpridos](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Contas MSA autónomas

Se não puder utilizar um gMSA, utilize uma conta de [serviço gerida autónoma](service-accounts-standalone-managed.md)(sMSA). sMSAs requerem pelo menos o Windows Server 2008R2. Ao contrário dos gMSAs, as SMSAs funcionam apenas num servidor. Podem ser usados para vários serviços nesse servidor.

### <a name="computer-account"></a>Conta informática

Se não puder utilizar um MSA, investigue utilizando uma [conta de computador.](service-accounts-computer.md) A conta LocalSystem é uma conta local predefinida que tem amplos privilégios no computador local, e funciona como a identidade de computador na rede.   
Os serviços que funcionam como recurso de rede de acesso à conta do Sistema Local utilizando as credenciais da conta do computador no formato <domain_name>\<computer_name> .

NT AUTHORITY\SYSTEM é o nome predefinido para a conta LocalSystem. Pode ser usado para iniciar um serviço e fornecer o contexto de segurança para esse serviço.

> [!NOTE]
> Quando uma conta de computador é utilizada, não é possível dizer qual o serviço no computador que está a utilizar essa conta, pelo que não pode auditar qual o serviço que está a esorte. 

### <a name="user-account"></a>Conta de utilizador

Se não puder utilizar um MSA, investigue utilizando [contas de utilizador](service-accounts-user-on-premises.md). As contas de utilizador podem ser uma conta de utilizador de domínio ou uma conta de utilizador local.

Uma conta de utilizador de domínio permite ao serviço tirar o máximo partido das funcionalidades de segurança do serviço do Windows e microsoft Ative Directory Domain Services. O serviço terá o acesso local e de rede concedido à conta. Terá também as permissões de quaisquer grupos dos quais a conta é membro. As contas de serviço de domínio suportam a autenticação mútua da Kerberos.

Uma conta de utilizador local (formato de nome: ".\UserName") existe apenas na base de dados SAM do computador anfitrião; não tem um objeto de utilizador nos Serviços de Domínio do Diretório Ativo. Uma conta local não pode ser autenticada pelo domínio. Assim, um serviço que funciona no contexto de segurança de uma conta de utilizador local não tem acesso a recursos de rede (exceto como utilizador anónimo). Os serviços em funcionamento no contexto de utilizador local não podem suportar a autenticação mútua da Kerberos na qual o serviço é autenticado pelos seus clientes. Por estas razões, as contas de utilizadores locais são normalmente inadequadas para serviços habilitados para diretórios.

> [!IMPORTANT]
> As contas de serviço não devem ser membros de quaisquer grupos privilegiados, uma vez que a adesão privilegiada ao grupo confere permissões que podem constituir um risco para a segurança. Cada serviço deve ter a sua própria conta de serviço para fins de auditoria e segurança.

## <a name="choose-the-right-type-of-service-account"></a>Escolha o tipo certo de conta de serviço


| Critérios| gMSA| sMSA| Conta informática| Conta de utilizador |
| - | - | - | - | - |
| App é executado em servidor único| Sim| Sim. Use um gMSA, se possível| Sim. Use um MSA, se possível| Sim. Utilize MSA, se possível. |
| App é executado em vários servidores| Yes| No| N.º A conta está ligada ao servidor| Sim. Utilize MSA, se possível. |
| App corre atrás de equilibradores de carga| Yes| No| Não| Yes. Utilize apenas se não puder utilizar um gMSA |
| App é executado no Windows Server 2008 R2| No| Sim| Sim. Utilize MSA, se possível.| Sim. Utilize MSA, se possível. |
| Executa no servidor Windows 2012| Sim| Sim. Use gMSA se possível| Sim. Use MSA, se possível| Sim. Utilize MSA, se possível. |
| Requisito para restringir a conta de serviço a um único servidor| No| Sim| Sim. Utilizar sMSA se possível| N.º |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Use registos de servidores e PowerShell para investigar

Pode utilizar registos de servidores para determinar quais os servidores e quantos servidores, uma aplicação está a ser iniciada.

Pode executar o seguinte comando PowerShell para obter uma lista da versão do Windows Server para todos os servidores da sua rede. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Encontre contas de serviço no local

Recomendamos que adicione um prefixo como "svc". Para todas as contas utilizadas como contas de serviço. Esta convenção de nomeação vai torná-los mais fáceis de encontrar e gerir. Considere também a utilização de um atributo descrição para a conta de serviço e o proprietário da conta de serviço, podendo ser um pseudónimo da equipa ou proprietário da equipa de segurança.

Encontrar contas de serviço no local é fundamental para garantir a sua segurança. E, pode ser difícil para contas não-MSA. Recomendamos que reveja todas as contas que tenham acesso aos seus importantes recursos no local e determine quais contas de computador ou utilizador podem estar a funcionar como contas de serviço. Também pode utilizar os seguintes métodos para encontrar contas.

* Os artigos para cada tipo de conta têm passos detalhados para encontrar esse tipo de conta. Para obter links para estes artigos, consulte a secção etapas seguintes deste artigo.

## <a name="document-service-accounts"></a>Contas de serviço de documentos

Uma vez encontradas as contas de serviço no seu ambiente no local, documente as seguintes informações sobre cada conta. 

* O dono. A pessoa responsável pela manutenção da conta.

* O propósito. O pedido que a conta representa, ou outro propósito. 

* Permissão. Que permissões tem, e deveria ter? E se algum grupo for membro?

* Perfil de risco. Qual é o risco para o seu negócio se esta conta está comprometida? Se for de alto risco, utilize um MSA.

* Tempo de vida previsto e atestado periódico. Quanto tempo espera que esta conta esteja ao vivo? Com que frequência o proprietário deve rever e atestar a necessidade permanente?

* Segurança de senha. Para contas de utilizador e computador local, onde a palavra-passe é armazenada. Certifique-se de que as palavras-passe são mantidas seguras e documente quem tem acesso. Considere usar a [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) para garantir senhas armazenadas. 

  

## <a name="next-steps"></a>Passos seguintes

Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)

