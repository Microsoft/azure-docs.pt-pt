---
title: Endurecimento de segurança nos anfitriões de máquinas virtuais AKS
description: Saiba mais sobre o endurecimento de segurança no hospedeiro DO O da AKS VM
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420905"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Reforço de segurança para o nó de agente AKS anfitrião OS

O Serviço Azure Kubernetes (AKS) é um serviço seguro compatível com as normas SOC, ISO, PCI DSS e HIPAA. Este artigo cobre o endurecimento de segurança aplicado aos anfitriões de máquinas virtuais AKS. Para obter mais informações sobre a segurança da AKS, consulte conceitos de [segurança para aplicações e clusters no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

> [!Note]
> Este documento é remepto apenas aos agentes linux na AKS.

Os clusters AKS são implantados em máquinas virtuais hospedeiras, que executam um SISTEMA otimizado de segurança que é utilizado para contentores em funcionamento no AKS. Este osso hospedeiro baseia-se numa imagem **Ubuntu 16.04.LTS** com endurecimento de segurança adicional e otimizações aplicadas (ver detalhes de endurecimento de segurança).

O objetivo do sistema de segurança endurecido do hospedeiro o SISTEMA é reduzir a área de ataque da superfície e otimizar para a implantação de contentores de forma segura.

> [!Important]
> O SISTEMA de segurança endurecido não é referenciado por CIS. Embora existam sobreposições com referências de CIS, o objetivo não é ser compatível com o CIS. O objetivo do endurecimento do OS do hospedeiro é convergir num nível de segurança consistente com os padrões internos de segurança do anfitrião da Microsoft.

## <a name="security-hardening-features"></a>Características de endurecimento de segurança

* A AKS fornece um sistema operativo de hospedas otimizado para a segurança por padrão. Não existe opção de selecionar um sistema operativo alternativo.

* O Azure aplica patches diários (incluindo patches de segurança) aos anfitriões de máquinas virtuais AKS. Algumas destas manchas exigirão um reboot, enquanto outras não. É responsável por agendar reboots de anfitriões de VM AKS, se necessário. Para obter orientações sobre como automatizar o remendo AKS, consulte [os remendos aks](https://docs.microsoft.com/azure/aks/node-updates-kured).

## <a name="what-is-configured"></a>O que está configurado

| CIS  | Descrição da auditoria|
|---|---|
| 1.1.1.1 |Certifique-se de que a montagem de sistemas de ficheiros cramfs está desativada|
| 1.1.1.2 |Certifique-se de que a montagem de sistemas de ficheiros freevxfs está desativada|
| 1.1.1.3 |Certifique-se de que a montagem de sistemas de ficheiros JFFS2 está desativada|
| 1.1.1.4 |Certifique-se de que a montagem de sistemas de ficheiros HFS está desativada|
| 1.1.1.5 |Certifique-se de que a montagem dos sistemas de ficheiros HFS Plus está desativada|
|1.4.3 |Garantir a autenticação necessária para o modo de utilizador único |
|1.7.1.2 |Certifique-se de que o banner de aviso de login local está configurado corretamente |
|1.7.1.3 |Certifique-se de que o banner de aviso de login remoto está configurado corretamente |
|1.7.1.5 |Certifique-se de que as permissões em /etc/problema estão configuradas |
|1.7.1.6 |Certifique-se de que as permissões em /etc/issue.net estão configuradas |
|2.1.5 |Certifique-se de que --streaming-ligação-inactivo-tempo-out não está definido para 0 |
|3.1.2 |Certifique-se de que o envio de redirecionamento de pacotes está desativado |
|3.2.1 |Certifique-se de que os pacotes de origem não são aceites |
|3.2.2 |Certifique-se de que os redirecionamentos do ICMP não são aceites |
|3.2.3 |Garantir que os redirecionamentos seguros do ICMP não são aceites |
|3.2.4 |Certifique-se de que pacotes suspeitos estão registados |
|3.3.1 |Certifique-se de que os anúncios do router IPv6 não são aceites |
|3.5.1 |Certifique-se de que o DCCP está desativado |
|3.5.2 |Certifique-se de que o SCTP está desativado |
|3.5.3 |Certifique-se de que rds está desativado |
|3.5.4 |Certifique-se de que o TIPC está desativado |
|4.2.1.2 |Certifique-se de que a exploração madeireira está configurada |
|5.1.2 |Certifique-se de que as permissões em /etc/crontab estão configuradas |
|5.2.4 |Certifique-se de que o encaminhamento SSH X11 está desativado |
|5.2.5 |Certifique-se de que o SSH MaxAuthTries está definido para 4 ou menos |
|5.2.8 |Certifique-se de que o login da raiz sSH está desativado |
|5.2.10 |Certifique-se de que o SSH PermitUserEnvironment está desativado |
|5.2.11 |Certifique-se de que apenas são utilizados algoritmos MAX aprovados |
|5.2.12 |Certifique-se de que o intervalo de tempo inativo ssh está configurado |
|5.2.13 |Certifique-se de que o SSH LoginGraceTime está definido para um minuto ou menos |
|5.2.15 |Certifique-se de que o banner de aviso SSH está configurado |
|5.3.1 |Certifique-se de que os requisitos de criação de passwords estão configurados |
|5.4.1.1 |Certifique-se de que a expiração da palavra-passe é de 90 dias ou menos |
|5.4.1.4 |Certifique-se de que o bloqueio de senha inativo é de 30 dias ou menos |
|5.4.4 |Certifique-se de que o utilizador por defeito é 027 ou mais restritivo |
|5.6 |Certifique-se de que o acesso ao comando su é restrito|

## <a name="additional-notes"></a>Notas adicionais
 
* Para reduzir ainda mais a área da superfície do ataque, alguns condutores de módulos de kernel desnecessários foram desativados no SISTEMA.

* O Sistema operativo reforçado de segurança é construído e mantido especificamente para aks e NÃO é suportado fora da plataforma AKS.

## <a name="next-steps"></a>Passos seguintes  

Consulte os seguintes artigos para obter mais informações sobre a segurança da AKS: 

[Serviço de Kubernetes do Azure (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerações de segurança da AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[As melhores práticas da AKS](https://docs.microsoft.com/azure/aks/best-practices)
