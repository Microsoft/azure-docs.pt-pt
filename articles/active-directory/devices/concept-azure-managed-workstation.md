---
title: Entenda as estações de trabalho seguras e gerenciadas pelo Azure-Azure Active Directory
description: Saiba mais sobre estações de trabalho seguras e gerenciadas pelo Azure e entenda por que elas são importantes.
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
ms.openlocfilehash: c26197a14e78b1cf1a1e078ba0145eca207206bf
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561947"
---
# <a name="understand-secure-azure-managed-workstations"></a>Entenda as estações de trabalho seguras e gerenciadas pelo Azure

Estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Se a segurança da estação de trabalho do cliente estiver comprometida, muitos controles de segurança e garantias poderão falhar ou ficar ineficazes.

Este documento explica o que você precisa para criar uma estação de trabalho segura, geralmente conhecida como uma estação de trabalho de acesso privilegiado (PAW). O artigo também contém instruções detalhadas para configurar os controles de segurança iniciais. Este guia descreve como a tecnologia baseada em nuvem pode gerenciar o serviço. Ele se baseia em recursos de segurança que foram introduzidos no Windows 10RS5, a ATP (proteção avançada contra ameaças) da Microsoft defender, Azure Active Directory e Microsoft Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e sua importância. Se você já estiver familiarizado com o conceito e quiser pular para a implantação, visite [implantar uma estação de trabalho segura](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso seguro à estação de trabalho é importante

A rápida adoção dos serviços de nuvem e a capacidade de trabalhar de qualquer lugar criou um novo método de exploração. Explorando controles de segurança fracos em dispositivos em que os administradores trabalham, os invasores podem obter acesso a recursos privilegiados.

Os ataques de uso indevido privilegiado e de cadeia de suprimentos estão entre os cinco principais métodos que os invasores usam para violar as organizações. Eles também são a segunda tática mais comumente detectada em incidentes relatados em 2018 de acordo com o [relatório de ameaças Verizon](https://enterprise.verizon.com/resources/reports/dbir/)e o [relatório de inteligência de segurança](https://aka.ms/sir).

A maioria dos invasores segue estas etapas:

1. Reconhecimento para encontrar uma maneira no, geralmente específico de um setor.
1. Análise para coletar informações e identificar a melhor maneira de invadirr uma estação de trabalho percebida como valor baixo.
1. Persistência para procurar um meio para mover-se [mais tarde](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Vazamento de dados confidenciais.

Durante o reconhecimento, os invasores frequentemente invadir dispositivos que parecem baixo risco ou undervalued. Eles usam esses dispositivos vulneráveis para localizar uma oportunidade de movimento lateral e para localizar usuários e dispositivos administrativos. Depois de obter acesso a funções de usuário privilegiadas, os invasores identificam dados de alto valor e exfiltrar com êxito esses dados.

![Padrão de comprometimento típico](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger seus dispositivos de computação contra tais ataques laterais. A solução isola o gerenciamento e os serviços de dispositivos de produtividade menos valiosos, dividindo a cadeia antes que o dispositivo que tem acesso a recursos de nuvem confidenciais possa ser infiltrated. A solução usa serviços nativos do Azure que fazem parte da pilha de Microsoft 365 Enterprise:

* Intune para gerenciamento de dispositivos e uma lista segura de aplicativos e URLs
* Piloto automático para instalação, implantação e atualização do dispositivo
* Azure AD para gerenciamento de usuários, acesso condicional e autenticação multifator
* Windows 10 (versão atual) para o atestado de integridade do dispositivo e a experiência do usuário
* Defender ATP para proteção, detecção e resposta de ponto de extremidade gerenciado pela nuvem
* PIM do Azure AD para gerenciar autorização e acesso privilegiado JIT (just-in-time) a recursos
* Log Analytics e sentinela para monitoramento e alertas

## <a name="who-benefits-from-a-secure-workstation"></a>Quem se beneficia de uma estação de trabalho segura?

Todos os usuários e operadores se beneficiam ao usar uma estação de trabalho segura. Um invasor que compromete um PC ou dispositivo pode representar todas as contas armazenadas em cache. Quando conectado ao dispositivo, eles também podem usar credenciais e tokens. Esse risco torna importante proteger os dispositivos que são usados para funções com privilégios, incluindo direitos administrativos. Dispositivos com contas privilegiadas são alvos para ataques de movimentação lateral e de elevação de privilégio. Essas contas podem ser usadas para uma variedade de ativos, como:

* Administrador de sistemas locais ou baseados em nuvem
* Estação de trabalho de desenvolvedor para sistemas críticos
* Administrador da conta de mídia social com alta exposição
* Estação de trabalho altamente confidencial, como um terminal de pagamento SWIFT
* Estação de trabalho lidando com segredos comerciais

Para reduzir o risco, você deve implementar controles de segurança elevados para estações de trabalho privilegiadas que fazem uso dessas contas. Para obter mais informações, consulte o [Guia de implantação de recurso Azure Active Directory](../fundamentals/active-directory-deployment-checklist-p2.md), o [roteiro do Office 365](https://aka.ms/o365secroadmap)e a [proteção do roteiro de acesso privilegiado](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Por que usar estações de trabalho dedicadas?

Embora seja possível adicionar segurança a um dispositivo existente, é melhor começar com uma base segura. Para colocar sua organização na melhor posição para manter um alto nível de segurança, comece com um dispositivo que você sabe que é seguro e implemente um conjunto de controles de segurança conhecidos.

Um número crescente de vetores de ataque por email e navegação na Web torna cada vez mais difícil ter certeza de que um dispositivo pode ser confiável. Este guia pressupõe que uma estação de trabalho dedicada seja isolada da produtividade, navegação e email padrão. A remoção de produtividade, navegação na Web e email de um dispositivo pode ter um impacto negativo na produtividade. No entanto, essa proteção normalmente é aceitável para cenários em que as tarefas de trabalho não exigem explicitamente isso e o risco de um incidente de segurança é alto.

> [!NOTE]
> A navegação na Web aqui refere-se ao acesso geral a sites arbitrários que podem ser uma atividade de alto risco. Essa navegação é distintamente diferente de usar um navegador da Web para acessar um pequeno número de sites administrativos conhecidos para serviços como o Azure, o Office 365, outros provedores de nuvem e aplicativos SaaS.

As estratégias de confinamento fortalecem a segurança aumentando o número e o tipo de controles que importam um invasor de obter ativos confidenciais de acesso. O modelo descrito neste artigo usa um design de privilégio em camadas e restringe privilégios administrativos a dispositivos específicos.

## <a name="supply-chain-management"></a>Gerenciamento de cadeia de fornecedores

Essencial para uma estação de trabalho segura é uma solução de cadeia de fornecedores em que você usa uma estação de trabalho confiável chamada "raiz de confiança". A tecnologia que deve ser considerada na seleção da raiz do hardware de confiança deve incluir as seguintes tecnologias incluídas em laptops modernos: 

* [Trusted Platform Module (TPM) 2,0](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-tpm)
* [Criptografia de Unidade de Disco BitLocker](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-bitlocker)
* [Inicialização segura de UEFI](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)
* [Drivers e firmware distribuídos por meio de Windows Update](https://docs.microsoft.com/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualização e política HVAC habilitados](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs)
* [Drivers e aplicativos política HVAC-Ready](https://docs.microsoft.com/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](https://docs.microsoft.com/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Proteção de e/s de DMA](https://docs.microsoft.com/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Proteção do sistema](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Espera moderna](https://docs.microsoft.com/windows-hardware/design/device-experiences/modern-standby)

Para essa solução, a raiz da confiança será implantada usando a tecnologia [Microsoft AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) com hardware que atenda aos requisitos técnicos modernos. Para proteger uma estação de trabalho, o Autopilot permite aproveitar os dispositivos Windows 10 com otimização de OEM da Microsoft. Esses dispositivos vêm em um estado válido conhecido do fabricante. Em vez de refazer a imagem de um dispositivo potencialmente inseguro, o piloto automático pode transformar um dispositivo Windows em um estado "pronto para o negócio". Ele aplica configurações e políticas, instala aplicativos e até mesmo altera a edição do Windows 10. Por exemplo, o piloto automático pode alterar a instalação do Windows de um dispositivo do Windows 10 pro para o Windows 10 Enterprise para que ele possa usar recursos avançados.

![Proteger níveis de estação de trabalho](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Perfis e funções de dispositivo

Esta orientação faz referência a vários perfis de segurança e funções que podem ajudá-lo a criar soluções mais seguras para usuários, desenvolvedores e pessoal de ti. Esses perfis equilibram a usabilidade e os riscos para usuários comuns que podem se beneficiar de uma estação de trabalho avançada ou segura. As configurações fornecidas aqui são baseadas nos padrões aceitos do setor. Esta orientação mostra como proteger o Windows 10 e reduzir os riscos associados ao comprometimento do dispositivo ou do usuário. Para aproveitar a tecnologia de hardware moderna e a raiz do dispositivo de confiança, usaremos [atestado de integridade do dispositivo](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), que está habilitado a partir do perfil de **alta segurança** . Esse recurso está presente para garantir que os invasores não possam ser persistentes durante a inicialização inicial de um dispositivo. Ele faz isso usando a política e a tecnologia para ajudar a gerenciar recursos e riscos de segurança.
![proteger os níveis de estação de trabalho](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Segurança básica** – uma estação de trabalho gerenciada e padrão fornece um bom ponto de partida para a maioria dos negócios de casa e de pequenas empresas. Esses dispositivos são registrados no Azure AD e gerenciados com o Intune. Esse perfil permite que os usuários executem qualquer aplicativo e naveguem por qualquer site. Uma solução antimalware como [o Microsoft defender](https://www.microsoft.com/windows/comprehensive-security) deve ser habilitada.

* **Segurança aprimorada** – essa solução de nível de entrada protegida é válida para usuários domésticos, pequenas empresas e desenvolvedores gerais.

   A estação de trabalho avançada é uma maneira baseada em políticas para aumentar a segurança do perfil de baixa segurança. Ele fornece um meio seguro para trabalhar com dados do cliente e também usar ferramentas de produtividade como email e navegação na Web. Você pode usar políticas de auditoria e o Intune para monitorar uma estação de trabalho aprimorada para o uso de perfil e o comportamento do usuário. Você implanta o perfil de estação de trabalho aprimorado com o script Windows10 (1809) e aproveita a proteção avançada contra malware usando a [ATP (proteção avançada contra ameaças)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Alta segurança** – o meio mais eficaz de reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de autoadministrar a estação de trabalho. A remoção de direitos administrativos locais é uma etapa que melhora a segurança, mas pode afetar a produtividade se implementada incorretamente. O perfil de alta segurança se baseia no perfil de segurança aprimorado com uma alteração considerável: a remoção do administrador local. Este perfil foi projetado para usuários de perfil alto: executivos, folha de pagamento e usuários de dados confidenciais, Aprovadores para serviços e processos.

   O usuário de alta segurança exige um ambiente mais controlado e, ao mesmo tempo, pode fazer atividades como email e navegação na Web em uma experiência simples de usar. Os usuários esperam que recursos como cookies, favoritos e outros atalhos funcionem. No entanto, esses usuários podem não exigir a capacidade de modificar ou depurar seus dispositivos. Eles também não precisam instalar drivers. O perfil de alta segurança é implantado usando o script de alta segurança-Windows10 (1809).

* **Especializado** – os invasores visam desenvolvedores e administradores de ti porque podem alterar sistemas de interesse para os invasores. A estação de trabalho especializada expande as políticas da estação de trabalho de alta segurança Gerenciando aplicativos locais e limitando sites. Ele também restringe os recursos de produtividade de alto risco, como ActiveX, Java, plug-ins de navegador e outros controles do Windows. Você implanta esse perfil com o script de SecurityBaseline DeviceConfiguration_NCSC-Windows10 (1803).

* **Protegido** – um invasor que compromete uma conta administrativa pode causar danos significativos nos negócios por roubo de dados, alteração de dados ou interrupção do serviço. Nesse estado protegido, a estação de trabalho habilita todos os controles de segurança e políticas que restringem o controle direto do gerenciamento de aplicativos locais. Uma estação de trabalho protegida não tem ferramentas de produtividade para que o dispositivo seja mais difícil de comprometer. Ele bloqueia o vetor mais comum para ataques de phishing: email e mídia social. A estação de trabalho protegida pode ser implantada com o script Secure Workstation-Windows10 (1809) SecurityBaseline.

   ![Estação de trabalho protegida](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece um administrador com uma estação de trabalho protegida que tem controle de aplicativo e proteção de aplicativo claros. A estação de trabalho usa o Credential Guard, o Device Guard e o Exploit Guard para proteger o host contra o comportamento mal-intencionado. Todos os discos locais também são criptografados com o BitLocker.

* **Isolado** – esse cenário personalizado e offline representa a extremidade extrema do espectro. Nenhum script de instalação é fornecido para esse caso. Talvez seja necessário gerenciar uma função comercialmente crítica que exija um sistema operacional herdado sem suporte ou não. Por exemplo, uma linha de produção de valor alto ou um sistema de suporte de vida. Como a segurança é crítica e os serviços de nuvem não estão disponíveis, você pode gerenciar e atualizar esses computadores manualmente ou com uma arquitetura de floresta Active Directory isolada, como o ESAE (ambiente de administrador de segurança avançado). Nessas circunstâncias, considere remover todo o acesso, exceto as verificações básicas de integridade do Intune e do ATP.

   * [Requisito de comunicação de rede do Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Requisito de comunicações de rede ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passos seguintes

[Implante uma estação de trabalho segura gerenciada pelo Azure](howto-azure-managed-workstation.md).
