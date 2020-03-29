---
title: Compreenda estações de trabalho seguras e geridas pelo Azure - Diretório Ativo Azure
description: Aprenda sobre estações de trabalho seguras e geridas pelo Azure e compreenda por que são importantes.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672665"
---
# <a name="understand-secure-azure-managed-workstations"></a>Compreender estações de trabalho seguras e geridas pelo Azure

As estações de trabalho isoladas e seguras são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Se a segurança da estação de trabalho do cliente estiver comprometida, muitos controlos de segurança e garantias podem falhar ou ser ineficazes.

Este documento explica o que precisa para construir uma estação de trabalho segura, muitas vezes conhecida como uma estação de trabalho privilegiada de acesso (PAW). O artigo também contém instruções detalhadas para a instalação de controlos de segurança iniciais. Esta orientação descreve como a tecnologia baseada na nuvem pode gerir o serviço. Baseia-se em capacidades de segurança que foram introduzidas no Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Ative Directory e Microsoft Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e a sua importância. Se já está familiarizado com o conceito e gostaria de saltar para a implantação, visite [Deploy a Secure Workstation](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso seguro à estação de trabalho é importante

A rápida adoção de serviços em nuvem e a capacidade de trabalhar a partir de qualquer lugar criaram um novo método de exploração. Ao explorar controlos de segurança fracos em dispositivos onde os administradores trabalham, os atacantes podem ter acesso a recursos privilegiados.

Os ataques privilegiados de utilização indevida e cadeia de fornecimento estão entre os cinco principais métodos que os atacantes usam para violar organizações. São também as segundas táticas mais detetadas em incidentes relatados em 2018, de acordo com o [relatório Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/)e o Relatório de Inteligência de [Segurança](https://aka.ms/sir).

A maioria dos agressores segue estes passos:

1. Reconhecimento para encontrar uma maneira de entrar, muitas vezes específico para uma indústria.
1. Análise para recolher informações e identificar a melhor maneira de se infiltrar numa estação de trabalho que é entendida como de baixo valor.
1. Persistência para procurar um meio de se mover [lateralmente.](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
1. Exfiltração de dados confidenciais e sensíveis.

Durante o reconhecimento, os atacantes infiltram-se frequentemente em dispositivos que parecem de baixo risco ou subvalorizados. Utilizam estes dispositivos vulneráveis para localizar uma oportunidade de movimento lateral e para encontrar utilizadores e dispositivos administrativos. Depois de terem acesso a funções privilegiadas de utilizador, os atacantes identificam dados de alto valor e exfiltram com sucesso esses dados.

![Padrão típico de compromisso](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger os seus dispositivos de computação de tais ataques laterais. A solução isola a gestão e os serviços de dispositivos de produtividade menos valiosos, quebrando a cadeia antes que o dispositivo que tenha acesso a recursos sensíveis em nuvem possa ser infiltrado. A solução utiliza serviços azure nativos que fazem parte da pilha Microsoft 365 Enterprise:

* Insintonização para gestão de dispositivos e uma lista segura de aplicações e URLs
* Piloto automático para configuração, implementação e atualização do dispositivo
* Azure AD para gestão de utilizadores, Acesso Condicional e autenticação de vários fatores
* Windows 10 (versão atual) para atestar a saúde do dispositivo e experiência do utilizador
* Defender ATP para proteção, deteção e resposta de pontofinal gerido pela nuvem
* Azure AD PIM para gestão da autorização e acesso privilegiado a recursos
* Log Analytics e Sentinel para monitorização e alerta

## <a name="who-benefits-from-a-secure-workstation"></a>Quem beneficia de uma estação de trabalho segura?

Todos os utilizadores e operadores beneficiam quando utilizam uma estação de trabalho segura. Um intruso que comprometa um PC ou dispositivo pode personificar todas as contas em cache. Quando acedem ao dispositivo, também podem usar credenciais e fichas. Este risco torna importante a segurança de dispositivos que são utilizados para papéis privilegiados, incluindo direitos administrativos. Dispositivos com contas privilegiadas são alvos de ataques de movimento lateral e de escalada de privilégios. Estas contas podem ser utilizadas para uma variedade de ativos tais como:

* Administrador de sistemas no local ou baseados em nuvem
* Estação de trabalho de desenvolvedores para sistemas críticos
* Administrador de conta de mídia social com alta exposição
* Estação de trabalho altamente sensível, como um terminal de pagamento SWIFT
* Workstation handling segredos comerciais

Para reduzir o risco, deve implementar controlos de segurança elevados para estações de trabalho privilegiadas que utilizam estas contas. Para mais informações, consulte o guia de implantação de [funcionalidades Azure Ative Directory,](../fundamentals/active-directory-deployment-checklist-p2.md) [o Roteiro do Office 365](https://aka.ms/o365secroadmap)e o Roteiro de [Acesso Privilegiado.](https://aka.ms/sparoadmap)

## <a name="why-use-dedicated-workstations"></a>Por que usar estações de trabalho dedicadas?

Embora seja possível adicionar segurança a um dispositivo existente, é melhor começar com uma base segura. Para colocar a sua organização na melhor posição para manter um alto nível de segurança, comece com um dispositivo que você sabe que é seguro e implemente um conjunto de controlos de segurança conhecidos.

Um número crescente de vetores de ataque através de e-mail e navegação na Web torna cada vez mais difícil ter certeza de que um dispositivo é de confiança. Este guia assume que uma estação de trabalho dedicada está isolada da produtividade padrão, navegação e e-mail. A remoção da produtividade, navegação na Web e e-mail de um dispositivo podem ter um impacto negativo na produtividade. No entanto, esta salvaguarda é tipicamente aceitável para cenários em que as tarefas de trabalho não a exijam explicitamente e o risco de um incidente de segurança é elevado.

> [!NOTE]
> A navegação na Web aqui refere-se ao acesso geral a sites arbitrários que podem ser uma atividade de alto risco. Esta navegação é distintamente diferente de usar um navegador web para aceder a um pequeno número de sites administrativos bem conhecidos para serviços como O Azure, Office 365, outros fornecedores de nuvem e aplicações SaaS.

As estratégias de contenção reforçam a segurança aumentando o número e o tipo de controlos que dissuadem um intruso de obter ativos sensíveis ao acesso. O modelo descrito neste artigo utiliza um design de privilégio santiva e restringe privilégios administrativos a dispositivos específicos.

## <a name="supply-chain-management"></a>Gestão da cadeia de abastecimento

Essencial para uma estação de trabalho segura é uma solução de cadeia de abastecimento onde se utiliza uma estação de trabalho de confiança chamada "raiz de confiança". A tecnologia que deve ser considerada na seleção da raiz do hardware trust deve incluir as seguintes tecnologias incluídas nos portáteis modernos: 

* [Módulo de Plataforma Fidedigna (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [Encriptação de Unidade BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Bota Segura UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Controladores e Firmware distribuídos através da Atualização do Windows](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualização e HVCI habilitados](/windows-hardware/design/device-experiences/oem-vbs)
* [Controladores e Aplicativos HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Proteção DMA I/O](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Guarda do Sistema](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Standby moderno](/windows-hardware/design/device-experiences/modern-standby)

Para esta solução, a raiz de confiança será implementada utilizando a tecnologia [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) com hardware que satisfaça os requisitos técnicos modernos. Para garantir uma estação de trabalho, o Autopilot permite-lhe alavancar os dispositivos Windows 10 otimizados pela Microsoft OEM. Estes dispositivos vêm num bom estado conhecido do fabricante. Em vez de reimaginar um dispositivo potencialmente inseguro, o Autopilot pode transformar um dispositivo Windows num estado "pronto para o negócio". Aplica definições e políticas, instala apps e até altera a edição do Windows 10. Por exemplo, o Autopilot pode alterar a instalação do Windows de um dispositivo do Windows 10 Pro para o Windows 10 Enterprise para que possa utilizar funcionalidades avançadas.

![Níveis seguros de estação de trabalho](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Funções e perfis do dispositivo

Esta orientação refere vários perfis de segurança e funções que podem ajudá-lo a criar soluções mais seguras para utilizadores, desenvolvedores e pessoal de TI. Estes perfis equilibram a usabilidade e os riscos para os utilizadores comuns que podem beneficiar de uma estação de trabalho melhorada ou segura. As configurações aqui fornecidas baseiam-se em padrões aceites pela indústria. Esta orientação mostra como endurecer o Windows 10 e reduzir os riscos associados ao compromisso do dispositivo ou do utilizador. Para tirar partido da tecnologia moderna de hardware e raiz do dispositivo trust, usaremos o [Attestation de Saúde](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)do Dispositivo , que está habilitado a partir do perfil de **Alta Segurança.** Esta capacidade está presente para garantir que os atacantes não podem ser persistentes durante a arranque inicial de um dispositivo. Fá-lo utilizando políticas e tecnologias para ajudar a gerir as funcionalidades e riscos de segurança.
![Níveis seguros de estação de trabalho](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Segurança Básica** – Uma estação de trabalho gerida e standard proporciona um bom ponto de partida para a maioria das casas e pequenas empresas. Estes dispositivos estão registados em Azure AD e geridos com intune. Este perfil permite que os utilizadores executem quaisquer aplicações e naveguem em qualquer website. Deve ser ativada uma solução anti-malware como o [Microsoft Defender.](https://www.microsoft.com/windows/comprehensive-security)

* **Segurança Reforçada** – Esta solução protegida de nível de entrada é boa para utilizadores domésticos, pequenos utilizadores de empresas e desenvolvedores gerais.

   O reforço do posto de trabalho é uma forma baseada na política de aumentar a segurança do baixo perfil de segurança. Fornece um meio seguro para trabalhar com os dados do cliente, ao mesmo tempo que utiliza ferramentas de produtividade como e-mail e navegação na Web. Pode utilizar políticas de auditoria e Intune para monitorizar uma estação de trabalho melhorada para o comportamento do utilizador e utilização do perfil. Implementa o perfil de estação de trabalho melhorado com o script Windows10 (1809) e tira partido da proteção avançada de malware utilizando a [Advanced Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Alta Segurança** – Os meios mais eficazes para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de autoadministração da estação de trabalho. A eliminação dos direitos administrativos locais é um passo que melhora a segurança, mas pode ter impacto na produtividade se for implementado incorretamente. O elevado perfil de segurança baseia-se no perfil de segurança reforçado com uma alteração considerável: a remoção do administrador local. Este perfil é projetado para utilizadores de alto perfil: executivos, utilizadores de folha de pagamento e dados sensíveis, aprovadores para serviços e processos.

   O utilizador de alta segurança exige um ambiente mais controlado, ao mesmo tempo que ainda pode fazer atividades como e-mail e navegação na Web numa experiência simples de usar. Os utilizadores esperam funcionalidades como cookies, favoritos e outros atalhos para funcionar. No entanto, estes utilizadores podem não necessitar da capacidade de modificar ou desincendear o seu dispositivo. Também não precisam de instalar os condutores. O alto perfil de segurança é implementado utilizando o script High Security - Windows10 (1809).

* **Specialized** – Os atacantes visam os desenvolvedores e administradores de TI porque podem alterar sistemas de interesse para os atacantes. A estação de trabalho especializada expande-se nas políticas da estação de trabalho de alta segurança, gerindo aplicações locais e limitando websites. Também restringe capacidades de produtividade de alto risco, tais como ActiveX, Java, plugins de navegador e outros controlos windows. Implementa este perfil com o script DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Seguro** – Um intruso que comprometa uma conta administrativa pode causar danos significativos às empresas por roubo de dados, alteração de dados ou perturbação do serviço. Neste estado endurecido, a estação de trabalho permite todos os controlos e políticas de segurança que restringem o controlo direto da gestão de aplicações locais. Uma estação de trabalho segura não tem ferramentas de produtividade, pelo que o dispositivo é mais difícil de comprometer. Bloqueia o vetor mais comum para ataques de phishing: e-mail e redes sociais. A estação de trabalho segura pode ser implantada com o script Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Posto de trabalho seguro](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece a um administrador uma estação de trabalho reforçada que tem um controlo de aplicação claro e um guarda de aplicações. A estação de trabalho usa guarda de credenciais, guarda de dispositivos e explora guarda para proteger o hospedeiro de comportamentos maliciosos. Todos os discos locais também estão encriptados com o BitLocker.

* **Isolado** – Este cenário personalizado e offline representa o extremo extremo do espectro. Não são fornecidos scripts de instalação para este caso. Pode ser necessário gerir uma função crítica de negócio que requer um sistema operativo legado não suportado ou não remendo. Por exemplo, uma linha de produção de alto valor ou um sistema de suporte de vida. Como a segurança é crítica e os serviços na nuvem estão indisponíveis, você pode gerir e atualizar estes computadores manualmente ou com uma arquitetura florestal isolada do Diretório Ativo, como o Enhanced Security Admin Environment (ESAE). Nestas circunstâncias, considere a remoção de todos os acessos, exceto os controlos básicos de insintonização e ATP.

   * [Requisito de comunicações de rede insinado](/intune/network-bandwidth-use)
   * [Requisito de comunicações de rede ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passos seguintes

[Implementar uma estação de trabalho segura gerida pelo Azure.](howto-azure-managed-workstation.md)
