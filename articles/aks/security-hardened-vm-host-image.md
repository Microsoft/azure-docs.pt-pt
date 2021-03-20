---
title: Endurecimento de segurança em anfitriões de máquinas virtuais AKS
description: Conheça o endurecimento de segurança no osa do anfitrião AKS VM
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 84b826ce33b5395db5bd38e883b3a0fb3425725b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86244043"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Endurecimento de segurança para o número de agente da AKS anfitrião OS

O Azure Kubernetes Service (AKS) é um serviço seguro que está em conformidade com os padrões SOC, ISO, PCI DSS e HIPAA. Este artigo cobre o endurecimento de segurança aplicado aos anfitriões de máquinas virtuais AKS. Para obter mais informações sobre a segurança da AKS, consulte [conceitos de segurança para aplicações e clusters no Serviço Azure Kubernetes (AKS)](./concepts-security.md).

> [!Note]
> Este documento é apenas para agentes Linux em AKS.

Os clusters AKS são implantados em máquinas virtuais hospedeiras, que executam um SISTEMA otimizado de segurança que é utilizado para contentores que funcionam em AKS. Este host OS baseia-se numa imagem **Ubuntu 16.04.LTS** com endurecimento de segurança adicional e otimizações aplicadas (ver detalhes de endurecimento de segurança).

O objetivo do hospedeiro de segurança endurecido é reduzir a área de superfície de ataque e otimizar a colocação de contentores de forma segura.

> [!Important]
> O sistema operativo reforçado de segurança NÃO é comparado com o CIS. Embora existam sobreposições com os critérios de referência da CEI, o objetivo não é ser compatível com a CEI. O objetivo para o endurecimento do OS anfitrião é convergir num nível de segurança consistente com os padrões de segurança internos da Microsoft.

## <a name="security-hardening-features"></a>Funcionalidades de endurecimento de segurança

* AKS fornece um sistema operativo de anfitrião otimizado por padrão. Não existe opção de selecionar um sistema operativo alternativo.

* O Azure aplica patches diários (incluindo patches de segurança) aos anfitriões de máquinas virtuais AKS. Alguns destes patches exigirão um reboot, enquanto outros não. Você é responsável por agendar reboots de anfitrião AKS VM conforme necessário. Para obter orientações sobre como automatizar o patching AKS, consulte [os nós AKS remendados](./node-updates-kured.md).

## <a name="what-is-configured"></a>O que está configurado

| CIS  | Descrição da auditoria|
|---|---|
| 1.1.1.1 |Certifique-se de que a montagem de sistemas de ficheiros de cramfs está desativada|
| 1.1.1.2 |Certifique-se de que a montagem de sistemas de ficheiros freevxfs está desativada|
| 1.1.1.3 |Certifique-se de que a montagem de sistemas de ficheiros JFFS2 está desativada|
| 1.1.1.4 |Certifique-se de que a montagem de sistemas de ficheiros HFS está desativada|
| 1.1.1.5 |Certifique-se de que a montagem de sistemas de ficheiros HFS Plus está desativada|
|1.4.3 |Garantir a autenticação necessária para o modo de utilizador único |
|1.7.1.2 |Certifique-se de que o banner de aviso de login local está configurado corretamente |
|1.7.1.3 |Certifique-se de que o banner de aviso de início de sessão remoto está configurado corretamente |
|1.7.1.5 |Certifique-se de que as permissões em /etc/emissão estão configuradas |
|1.7.1.6 |Certifique-se de que as permissões em /etc/issue.net estão configuradas |
|2.1.5 |Certifique-se de que --streaming-ligação-inactivo-tempo limite não está definido para 0 |
|3.1.2 |Certifique-se de que o envio de redirecionamento de pacotes está desativado |
|3.2.1 |Certifique-se de que os pacotes rotados de origem não são aceites |
|3.2.2 |Garantir que os redirecionamentos do ICMP não são aceites |
|3.2.3 |Garantir que os redirecionamentos ICMP seguros não são aceites |
|3.2.4 |Certifique-se de que os pacotes suspeitos são registados |
|3.3.1 |Certifique-se de que os anúncios do router IPv6 não são aceites |
|3.5.1 |Certifique-se de que o DCCP está desativado |
|3.5.2 |Garantir que a SCTP está desativada |
|3.5.3 |Certifique-se de que o RDS está desativado |
|3.5.4 |Certifique-se de que o TIPC está desativado |
|4.2.1.2 |Certifique-se de que o registo está configurado |
|5.1.2 |Certifique-se de que as permissões em /etc/crontab estão configuradas |
|5.2.4 |Certifique-se de que o encaminhamento SSH X11 está desativado |
|5.2.5 |Certifique-se de que o SSH MaxAuthTries está definido para 4 ou menos |
|5.2.8 |Certifique-se de que o login de raiz SSH está desativado |
|5.2.10 |Garantir que o SSH PermitUserEnvironment é desativado |
|5.2.11 |Certifique-se de que apenas os algoritmos MAX aprovados são usados |
|5.2.12 |Certifique-se de que o intervalo de tempo de marcha lenta está configurado |
|5.2.13 |Certifique-se de que o SSH LoginGraceTime está definido para um minuto ou menos |
|5.2.15 |Certifique-se de que o banner de aviso SSH está configurado |
|5.3.1 |Certifique-se de que os requisitos de criação de palavras-passe estão configurados |
|5.4.1.1 |Certifique-se de que a expiração da palavra-passe é de 90 dias ou menos |
|5.4.1.4 |Certifique-se de que o bloqueio de senha inativo é de 30 dias ou menos |
|5.4.4 |Certifique-se de que o utilizador padrão umask é 027 ou mais restritivo |
|5,6 |Garantir que o acesso ao comando su é restrito|

## <a name="additional-notes"></a>Notas adicionais
 
* Para reduzir ainda mais a área da superfície de ataque, alguns condutores de módulos de núcleo desnecessários foram desativados no SISTEMA.

* O sistema operativo de segurança endurecido é construído e mantido especificamente para aKS e NÃO é suportado fora da plataforma AKS.

## <a name="next-steps"></a>Passos seguintes  

Consulte os seguintes artigos para obter mais informações sobre a segurança da AKS: 

[Azure Kubernetes Service (AKS)](./intro-kubernetes.md)

[Considerações de segurança da AKS ](./concepts-security.md)

[Melhores práticas da AKS ](./best-practices.md)
