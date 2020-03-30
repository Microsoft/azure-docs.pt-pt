---
title: Endurecimento de segurança nos anfitriões de máquinas virtuais AKS
description: Saiba mais sobre o endurecimento de segurança no hospedeiro DO O da AKS VM
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594385"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Endurecimento de segurança nos anfitriões de máquinas virtuais AKS 

O Serviço Azure Kubernetes (AKS) é um serviço seguro compatível com as normas SOC, ISO, PCI DSS e HIPAA. Este artigo cobre o endurecimento de segurança aplicado aos anfitriões de máquinas virtuais AKS. Para obter mais informações sobre a segurança da AKS, consulte conceitos de [segurança para aplicações e clusters no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

Os clusters AKS são implantados em máquinas virtuais hospedeiras, que executam um SISTEMA otimizado de segurança. Este osso hospedeiro baseia-se atualmente numa imagem Ubuntu 16.04.LTS com um conjunto de medidas adicionais de endurecimento de segurança aplicadas (ver detalhes de endurecimento de segurança).   

O objetivo do hospedeiro de segurança endurecido o SISTEMA é reduzir a área de ataque da superfície e permitir a implantação de contentores de forma segura. 

> [!Important]
> O SISTEMA de segurança endurecido não é referenciado por CIS. Embora existam sobreposições com referências de CIS, o objetivo não é ser compatível com o CIS. O objetivo do endurecimento do OS do hospedeiro é convergir num nível de segurança consistente com os padrões internos de segurança do anfitrião da Microsoft. 

## <a name="security-hardening-features"></a>Características de endurecimento de segurança 

* A AKS fornece um sistema operativo de hospedas otimizado para a segurança por padrão. Não existe uma opção atual para selecionar um sistema operativo alternativo. 

* O Azure aplica patches diários (incluindo patches de segurança) aos anfitriões de máquinas virtuais AKS. Algumas destas manchas exigirão um reboot, enquanto outras não. É responsável por agendar reboots de anfitriões de VM AKS, se necessário. Para obter orientações sobre como automatizar o remendo AKS, consulte [os remendos aks](https://docs.microsoft.com/azure/aks/node-updates-kured).

Abaixo está um resumo do trabalho de endurecimento de imagem que é implementado no AKS-Engine para produzir o hospedeiro otimizado de segurança OS. O trabalho foi implementado [neste projeto GitHub.](https://github.com/Azure/aks-engine/projects/7)  

O AKS-Engine não promove nem adere a qualquer norma de segurança específica neste momento, mas os IDs de auditoria do CIS (Center for Internet Security) são fornecidos para conveniência quando aplicável. 

## <a name="whats-configured"></a>O que está configurado?

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

* O SISTEMA de segurança não é suportado fora da plataforma AKS. 

## <a name="next-steps"></a>Passos seguintes  

Consulte os seguintes artigos para obter mais informações sobre a segurança da AKS: 

[Serviço de Kubernetes do Azure (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerações de segurança da AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[As melhores práticas da AKS](https://docs.microsoft.com/azure/aks/best-practices)
