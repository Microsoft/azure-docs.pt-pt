---
title: Compreenda estações de trabalho seguras e geridas pelo Azure - Azure Ative Directory
description: Aprenda sobre estações de trabalho seguras geridas pelo Azure e compreenda por que são importantes.
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
ms.openlocfilehash: 6837bbdb63caf0fb1ecb3f6e520d5f3623483b44
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083240"
---
# <a name="understand-secure-azure-managed-workstations"></a>Compreenda estações de trabalho seguras e geridas pelo Azure

Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Se a segurança da estação de trabalho dos clientes estiver comprometida, muitos controlos de segurança e garantias podem falhar ou ser ineficazes.

Este documento explica o que precisa para construir uma estação de trabalho segura, muitas vezes conhecida como uma estação de trabalho privilegiada de acesso (PAW). O artigo contém também instruções detalhadas para a instalação dos controlos de segurança iniciais. Esta orientação descreve como a tecnologia baseada na nuvem pode gerir o serviço. Baseia-se nas capacidades de segurança que foram introduzidas no Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Ative Directory e Microsoft Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e a sua importância. Se já está familiarizado com o conceito e gostaria de saltar para a implementação, visite [Implementar uma estação de trabalho segura.](howto-azure-managed-workstation.md)

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso seguro à estação de trabalho é importante

A rápida adoção de serviços em nuvem e a capacidade de trabalhar a partir de qualquer lugar criaram um novo método de exploração. Ao explorar controlos de segurança fracos em dispositivos onde os administradores trabalham, os atacantes podem ter acesso a recursos privilegiados.

Os ataques privilegiados e de cadeia de abastecimento estão entre os cinco principais métodos que os atacantes usam para violar organizações. São também a segunda tática mais detetada em incidentes relatados em 2018, de acordo com o [relatório Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/)e o Relatório de Inteligência de [Segurança.](https://aka.ms/sir)

A maioria dos atacantes segue estes passos:

1. Reconhecimento para encontrar uma maneira de entrar, muitas vezes específica para uma indústria.
1. Análise para recolher informações e identificar a melhor maneira de se infiltrar num posto de trabalho que é visto como de baixo valor.
1. Persistência em procurar um meio de se mover [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltração de dados confidenciais e sensíveis.

Durante o reconhecimento, os atacantes infiltram-se frequentemente em dispositivos que parecem de baixo risco ou subvalorizados. Utilizam estes dispositivos vulneráveis para localizar uma oportunidade de movimento lateral e para encontrar utilizadores e dispositivos administrativos. Depois de terem acesso a funções privilegiadas de utilizador, os atacantes identificam dados de alto valor e exfiltram esses dados com sucesso.

![Padrão de compromisso típico](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger os seus dispositivos informáticos de tais ataques laterais. A solução isola a gestão e os serviços de dispositivos de produtividade menos valiosos, quebrando a cadeia antes que o dispositivo que tenha acesso a recursos sensíveis da nuvem possa ser infiltrado. A solução utiliza serviços nativos da Azure que fazem parte da stack da Microsoft 365 Enterprise:

* Sintonização para gestão de dispositivos e uma lista segura de aplicações e URLs
* Piloto automático para configuração, implantação e atualização do dispositivo
* Azure AD para gestão de utilizadores, Acesso Condicional e autenticação de vários fatores
* Windows 10 (versão atual) para atestado de saúde do dispositivo e experiência do utilizador
* Defender ATP para proteção, deteção e resposta geridas pela nuvem
* Azure AD PIM para gestão da autorização e acesso privilegiado (JIT) a recursos
* Log Analytics e Sentinel para monitorização e alerta

## <a name="who-benefits-from-a-secure-workstation"></a>Quem beneficia de um posto de trabalho seguro?

Todos os utilizadores e operadores beneficiam quando utilizam uma estação de trabalho segura. Um intruso que comprometa um PC ou dispositivo pode personificar todas as contas em cache. Quando iniciados no dispositivo, também podem usar credenciais e fichas. Este risco torna importante a segurança de dispositivos que são utilizados para funções privilegiadas, incluindo direitos administrativos. Dispositivos com contas privilegiadas são alvos de movimentos laterais e ataques de escalada de privilégios. Estas contas podem ser utilizadas para uma variedade de ativos, tais como:

* Administrador de sistemas no local ou em nuvem
* Estação de trabalho do desenvolvedor para sistemas críticos
* Administrador de conta de redes sociais com elevada exposição
* Estações de trabalho altamente sensíveis, como um terminal de pagamento SWIFT
* Estação de trabalho que lida com segredos comerciais

Para reduzir o risco, deve implementar controlos de segurança elevados para estações de trabalho privilegiadas que façam uso destas contas. Para obter mais informações, consulte o guia de implementação de funcionalidades do [Azure Ative Directory](../fundamentals/active-directory-deployment-checklist-p2.md), [o roteiro microsoft 365](/microsoft-365/security/office-365-security/security-roadmap)e o roteiro de acesso privilegiado de [segurança).](/windows-server/identity/securing-privileged-access/securing-privileged-access)

## <a name="why-use-dedicated-workstations"></a>Porquê usar postos de trabalho dedicados?

Embora seja possível adicionar segurança a um dispositivo existente, é melhor começar com uma fundação segura. Para colocar a sua organização na melhor posição para manter um alto nível de segurança, comece com um dispositivo que sabe ser seguro e implemente um conjunto de controlos de segurança conhecidos.

Um número crescente de vetores de ataque através de e-mail e navegação na Web torna cada vez mais difícil ter certeza de que um dispositivo pode ser confiável. Este guia assume que uma estação de trabalho dedicada está isolada da produtividade padrão, navegação e e-mail. A remoção da produtividade, navegação na Web e e-mail de um dispositivo pode ter um impacto negativo na produtividade. No entanto, esta salvaguarda é tipicamente aceitável para cenários em que as tarefas de trabalho não o exigem explicitamente e o risco de um incidente de segurança é elevado.

> [!NOTE]
> A navegação na Web aqui refere-se ao acesso geral a sites arbitrários que podem ser uma atividade de alto risco. Esta navegação é distintamente diferente de usar um navegador web para aceder a um pequeno número de sites administrativos bem conhecidos para serviços como Azure, Microsoft 365, outros fornecedores de nuvem e aplicações SaaS.

As estratégias de contenção reforçam a segurança aumentando o número e o tipo de controlos que impedem um intruso de aceder a ativos sensíveis. O modelo descrito neste artigo usa um design de privilégio hierarquizado e restringe privilégios administrativos a dispositivos específicos.

## <a name="supply-chain-management"></a>Gestão da cadeia de abastecimento

Essencial para uma estação de trabalho segura é uma solução de cadeia de abastecimento onde se utiliza uma estação de trabalho de confiança chamada "raiz da confiança". A tecnologia que deve ser considerada na seleção da raiz do hardware trust deve incluir as seguintes tecnologias incluídas nos portáteis modernos: 

* [Módulo de Plataforma Fidedigna (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [Encriptação de Unidade BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Bota Segura UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Controladores e Firmware distribuídos através da atualização do Windows](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualização e HVCI Habilitados](/windows-hardware/design/device-experiences/oem-vbs)
* [Motoristas e Apps HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Proteção DMA I/O](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Guarda do Sistema](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Moderno Standby](/windows-hardware/design/device-experiences/modern-standby)

Para esta solução, a raiz de confiança será implementada utilizando a tecnologia [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) com hardware que satisfaz os requisitos técnicos modernos. Para garantir uma estação de trabalho, o Autopilot permite-lhe alavancar dispositivos Windows 10 otimizados pelo Microsoft OEM. Estes dispositivos vêm em bom estado conhecido do fabricante. Em vez de reimaging de um dispositivo potencialmente inseguro, o Autopilot pode transformar um dispositivo Windows num estado "pronto para o negócio". Aplica configurações e políticas, instala aplicações e até altera a edição do Windows 10. Por exemplo, o Autopilot pode alterar a instalação do Windows de um dispositivo do Windows 10 Pro para o Windows 10 Enterprise para que possa utilizar funcionalidades avançadas.

:::image type="complex" source="./media/concept-azure-managed-workstation/supplychain.png" alt-text="Diagrama mostrando o ciclo de vida de uma estação de trabalho segura." border="false":::
Perto do topo do diagrama, um vendedor de dispositivos é fotografado. As setas apontam desse vendedor para um cliente que comprou a estação de trabalho e para um camião que está rotulado Cumprir e entregar. Do caminhão, uma seta aponta para uma imagem marcada Implementar que fotografa uma pessoa usando uma estação de trabalho. Uma experiência de self service com a seta estende-se dessa pessoa a um ecrã rotulado Ready para o negócio. Por baixo desse ecrã, os ícones que estão rotulados Geridos protegidos são retratados. Uma seta marcada como Uso de estado constante, Gerencie e mantenha os pontos atuais do ecrã para um ícone de fim de vida e para um ícone de reset de reposição de rutura. Uma seta final volta do ícone de break-fix para o ecrã pronto para o negócio.
:::image-end:::

## <a name="device-roles-and-profiles"></a>Funções e perfis do dispositivo

Esta orientação refere vários perfis de segurança e funções que podem ajudá-lo a criar soluções mais seguras para utilizadores, desenvolvedores e pessoal de TI. Estes perfis equilibram a usabilidade e os riscos para os utilizadores comuns que podem beneficiar de uma estação de trabalho melhorada ou segura. As configurações de configurações fornecidas aqui baseiam-se em padrões aceites pela indústria. Esta orientação mostra como endurecer o Windows 10 e reduzir os riscos associados ao dispositivo ou ao compromisso do utilizador. Para tirar partido da moderna tecnologia de hardware e raiz do dispositivo trust, usaremos [o Attestation Device Health](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), que está habilitado a partir do perfil de Alta **Segurança.** Esta capacidade encontra-se para garantir que os atacantes não podem ser persistentes durante o arranque precoce de um dispositivo. Fá-lo utilizando políticas e tecnologias para ajudar a gerir funcionalidades e riscos de segurança.

:::image type="content" source="./media/concept-azure-managed-workstation/seccon-levels.png" alt-text="Diagrama mostrando o ciclo de vida de uma estação de trabalho segura." border="false":::

* **Segurança Básica** – Uma estação de trabalho gerida e padrão proporciona um bom ponto de partida para a maioria do uso doméstico e de pequenas empresas. Estes dispositivos estão registados em Azure AD e geridos com o Intune. Este perfil permite que os utilizadores executem quaisquer aplicações e naveguem em qualquer website. Uma solução anti-malware como [o Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve ser ativada.

* **Segurança Reforçada** – Esta solução protegida de nível de entrada é boa para utilizadores domésticos, pequenos utilizadores de empresas e desenvolvedores gerais.

   O reforço da estação de trabalho é uma forma baseada em políticas para aumentar a segurança do baixo perfil de segurança. Fornece um meio seguro para trabalhar com os dados dos clientes, ao mesmo tempo que utiliza ferramentas de produtividade como e-mail e navegação na Web. Pode utilizar políticas de auditoria e Intune para monitorizar uma estação de trabalho melhorada para o comportamento do utilizador e utilização do perfil. Implementa o perfil de estação de trabalho melhorado com o script Windows10 (1809) e tira partido da proteção avançada de malware utilizando [a Advanced Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Alta Segurança** – O meio mais eficaz para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de autoadministrar a estação de trabalho. A eliminação dos direitos administrativos locais é um passo que melhora a segurança, mas pode ter impacto na produtividade se implementada incorretamente. O elevado perfil de segurança baseia-se no perfil de segurança reforçado com uma mudança considerável: a remoção da administração local. Este perfil destina-se a utilizadores de alto perfil: executivos, utilizadores de folha de pagamento e dados sensíveis, aprovadores de serviços e processos.

   O utilizador de alta segurança exige um ambiente mais controlado, ao mesmo tempo que ainda é capaz de fazer atividades como e-mail e navegação na Web numa experiência simples de usar. Os utilizadores esperam que funcionalidades como cookies, favoritos e outros atalhos funcionem. No entanto, estes utilizadores podem não necessitar da capacidade de modificar ou desorixar o seu dispositivo. Também não precisam de instalar condutores. O alto perfil de segurança é implementado utilizando o script High Security - Windows10 (1809).

* **Especializados** – Os atacantes visam os desenvolvedores e administradores de TI porque podem alterar sistemas de interesse para os atacantes. A estação de trabalho especializada expande-se sobre as políticas da estação de trabalho de alta segurança, gerindo aplicações locais e limitando websites. Também restringe as capacidades de produtividade de alto risco, tais como ActiveX, Java, plugins de navegador e outros controlos windows. Implementa este perfil com o script DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Seguro** – Um intruso que comprometa uma conta administrativa pode causar danos significativos no negócio por roubo de dados, alteração de dados ou interrupção do serviço. Neste estado endurecido, a estação de trabalho permite todos os controlos de segurança e políticas que restringem o controlo direto da gestão de aplicações locais. Uma estação de trabalho segura não tem ferramentas de produtividade, pelo que o dispositivo é mais difícil de comprometer. Bloqueia o vetor mais comum para ataques de phishing: e-mail e redes sociais. A estação de trabalho segura pode ser implantada com o script Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Estação de trabalho segura](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece a um administrador uma estação de trabalho reforçada que tem controlo de aplicações claro e guarda de aplicações. A estação de trabalho utiliza guarda credencial, proteção do dispositivo e proteção para proteger o hospedeiro de comportamentos maliciosos. Todos os discos locais também são encriptados com BitLocker.

* **Isolado** – Este cenário personalizado e offline representa a extremidade extrema do espectro. Não são fornecidos scripts de instalação para este caso. Poderá ser necessário gerir uma função crítica de negócios que exija um sistema operativo não suportado ou não remutado. Por exemplo, uma linha de produção de alto valor ou um sistema de suporte de vida. Como a segurança é crítica e os serviços na nuvem não estão disponíveis, pode gerir e atualizar estes computadores manualmente ou com uma arquitetura florestal isolada do Ative Directory, como o Enhanced Security Admin Environment (ESAE). Nestas circunstâncias, considere remover todos os acessos, exceto os controlos básicos de saúde Intune e ATP.

   * [Requisito de comunicações de rede Intune](/intune/network-bandwidth-use)
   * [Requisito de comunicações de rede ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passos seguintes

[Implementar uma estação de trabalho gerida pelo Azure segura](howto-azure-managed-workstation.md).