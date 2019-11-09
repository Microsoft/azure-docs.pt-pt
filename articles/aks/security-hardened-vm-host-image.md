---
title: Proteção de segurança em hosts de máquina virtual AKS
description: Saiba mais sobre a proteção de segurança no sistema operacional host de VM AKS
services: container-service
author: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 8b7e50fdc02ab47c50cecb95073f1b51393db898
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885636"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Proteção de segurança em hosts de máquina virtual AKS 

O AKS (serviço kubernetes do Azure) é um serviço seguro compatível com os padrões SOC, ISO, PCI DSS e HIPAA. Este artigo aborda a proteção de segurança aplicada aos hosts de máquina virtual AKS. Para obter mais informações sobre a segurança do AKS, consulte [conceitos de segurança para aplicativos e clusters no AKs (serviço kubernetes do Azure)](https://docs.microsoft.com/azure/aks/concepts-security).

Os clusters AKS são implantados em máquinas virtuais de host, que executam um sistema operacional otimizado para segurança. Esse sistema operacional host atualmente é baseado em uma imagem Ubuntu 16.04. LTS com um conjunto de etapas de proteção de segurança adicionais aplicadas (consulte detalhes de proteção de segurança).   

O objetivo do sistema operacional de host protegido por segurança é reduzir a área de ataque e permitir a implantação de contêineres de maneira segura. 

> [!Important]
> O sistema operacional protegido por segurança não é um parâmetro de comparação de CIS. Embora haja sobreposições com benchmarks de CIS, o objetivo não é ser compatível com o CIS. O objetivo da proteção do sistema operacional do host é convergir em um nível de segurança consistente com os próprios padrões internos de segurança de host da Microsoft. 

## <a name="security-hardening-features"></a>Recursos de proteção de segurança 

* O AKS fornece um sistema operacional de host otimizado para segurança por padrão. Não há nenhuma opção atual para selecionar um sistema operacional alternativo. 

* O Azure aplica patches diários (incluindo patches de segurança) a hosts de máquina virtual AKS. Alguns desses patches exigirão uma reinicialização, enquanto outros não vão. Você é responsável por agendar reinicializações de host de VM AKS conforme necessário. Para obter orientação sobre como automatizar a aplicação de patches do AKS, consulte [patching AKs Nodes](https://docs.microsoft.com/azure/aks/node-updates-kured).

Veja abaixo um resumo do trabalho de proteção de imagem que é implementado no AKS para produzir o sistema operacional de host otimizado para segurança. O trabalho foi implementado [neste projeto do GitHub](https://github.com/Azure/aks-engine/projects/7).  

O AKS-Engine não promove ou adere a nenhum padrão de segurança específico no momento, mas as IDs de auditoria do CIS (Center for Internet Security) são fornecidas para sua conveniência, quando aplicável. 

## <a name="whats-configured"></a>O que está configurado?

| ICS  | Descrição da auditoria| 
|---|---|
| 1.1.1.1 |Verifique se a montagem dos sistemas de CramFs está desabilitada|
| 1.1.1.2 |Verifique se a montagem dos sistemas de freevxfs está desabilitada|
| 1.1.1.3 |Verifique se a montagem dos sistemas de JFFS2 está desabilitada|
| 1.1.1.4 |Verifique se a montagem dos sistemas de HFS está desabilitada|
| 1.1.1.5 |Certifique-se de que a montagem de HFS mais sistemas de sistema esteja desabilitada|
|1.4.3 |Garantir a autenticação necessária para o modo de usuário único |
|1.7.1.2 |Verifique se a faixa de aviso de logon local está configurada corretamente |
|1.7.1.3 |Verifique se a faixa de aviso de logon remoto está configurada corretamente |
|1.7.1.5 |Verifique se as permissões em/etc/issue estão configuradas |
|1.7.1.6 |Verifique se as permissões em/etc/issue.net estão configuradas |
|2.1.5 |Verifique se--streaming-Connection-idle-timeout não está definido como 0 |
|3.1.2 |Verificar se o envio de redirecionamento de pacotes está desabilita |
|3.2.1 |Verifique se os pacotes roteados de origem não são aceitos |
|3.2.2 |Verifique se os redirecionamentos ICMP não são aceitos |
|3.2.3 |Verifique se os redirecionamentos ICMP seguros não são aceitos |
|3.2.4 |Garantir que os pacotes suspeitos sejam registrados |
|3.3.1 |Verifique se os anúncios do roteador IPv6 não são aceitos |
|3.0 |Verifique se o DCCP está desabilitado |
|3.5.2 |Verifique se SCTP está desabilitado |
|3.5.3 |Garantir que o RDS esteja desabilitado |
|3.5.4 |Verifique se o TIPC está desabilitado |
|4.2.1.2 |Verifique se o registro em log está configurado |
|5.1.2 |Verifique se as permissões em/etc/crontab estão configuradas |
|5.2.4 |Verifique se o encaminhamento do SSH X11 está desabilitado |
|5.2.5 |Verifique se o SSH MaxAuthTries está definido como 4 ou menos |
|5.2.8 |Verifique se o logon raiz do SSH está desabilitado |
|5.2.10 |Verifique se o SSH PermitUserEnvironment está desabilitado |
|5.2.11 |Garantir que apenas algoritmos MÁXIMOs aprovados sejam usados |
|5.2.12 |Verificar se o intervalo de tempo limite de ociosidade de SSH está configurado |
|5.2.13 |Verifique se o SSH LoginGraceTime está definido para um minuto ou menos |
|5.2.15 |Verificar se a faixa de aviso SSH está configurada |
|5.3.1 |Verifique se os requisitos de criação de senha estão configurados |
|5.4.1.1 |Garantir que a expiração da senha seja de 90 dias ou menos |
|5.4.1.4 |Garantir que o bloqueio de senha inativo seja de 30 dias ou menos |
|5.4.4 |Garantir que o usuário padrão umask seja 027 ou mais restritivo |
|5,6 |Verifique se o acesso ao comando su está restrito|

## <a name="additional-notes"></a>Notas adicionais
 
* Para reduzir ainda mais a área de superfície de ataque, alguns drivers de módulo de kernel desnecessários foram desabilitados no sistema operacional. 

* Não há suporte para o sistema operacional protegido de segurança fora da plataforma AKS. 

## <a name="next-steps"></a>Passos seguintes  

Consulte os artigos a seguir para obter mais informações sobre a segurança do AKS: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerações de segurança do AKS](https://docs.microsoft.com/azure/aks/concepts-security)

[Práticas recomendadas do AKS](https://docs.microsoft.com/azure/aks/best-practices)
