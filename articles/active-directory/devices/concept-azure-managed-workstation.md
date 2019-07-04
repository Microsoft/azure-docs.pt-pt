---
title: Compreender seguras e gerida pelo Azure estações de trabalho - Azure Active Directory
description: Saiba mais sobre as estações de trabalho seguras e gerida pelo Azure e compreender por que elas são importantes.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491599"
---
# <a name="understand-secure-azure-managed-workstations"></a>Compreender as estações de trabalho seguras e gerida pelo Azure

Isolada protegidas e estações de trabalho são extremamente importantes para garantir a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de críticos de serviço. Se a segurança da estação de trabalho de cliente for comprometida, muitos controles de segurança e garantias podem falhar ou não tenha efeito.

Este documento explica o que precisa para a criação de uma estação de trabalho segura, muitas vezes conhecida como uma estação de trabalho de acesso privilegiado (PAW). O artigo também contém instruções detalhadas para configurar controlos de segurança inicial. Estas orientações descrevem como na cloud com base em tecnologia pode gerir o serviço. Ele se baseia nas capacidades de segurança que foram introduzidas no Windows 10RS5, proteção de ameaças avançadas do Microsoft Defender (ATP), Azure Active Directory e Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e sua importância. Se já estiver familiarizado com o conceito e gostaria de ignorar a implementação, visite [implementar uma estação de trabalho seguro](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso de estação de trabalho segura é importante

A rápida adoção de serviços cloud e a capacidade de trabalhar a partir de qualquer local tiver criado um novo método de exploração. Ao explorar os controles de segurança frágeis em dispositivos em que os administradores trabalham, os invasores podem ganhar acesso a recursos com privilégios.

Uso indevido de privilégios e ataques de cadeia de fornecimento estão entre os métodos de cinco principais usados pelos invasores para as organizações de violações. Elas também são a segunda mais comumente detectadas táticas em incidentes reportados em 2018 de acordo com o [relatório de ameaças da Verizon](https://enterprise.verizon.com/resources/reports/dbir/)e o [relatório de inteligência de segurança](https://aka.ms/sir).

A maioria dos invasores, siga estes passos:

1. Reconhecimento encontrar uma maneira, muitas vezes específicos da indústria.
1. Análise para coletar informações e identificar a melhor forma para se infiltrarem numa estação de trabalho que é percebida como valor baixo.
1. A persistência para procurar um meio mover [-se lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Filtragem de dados confidenciais e delicadas.

Durante o reconhecimento, os atacantes frequentemente se infiltrarem em dispositivos que parecem baixo risco ou undervalued. Utilizam estes dispositivos vulneráveis para localizar uma oportunidade para movimento lateral e para localizar os utilizadores administrativos e de dispositivos. Depois de obterem acesso a funções de utilizador com privilégios, os atacantes identificam dados de valor elevado e com êxito a retirar esses dados.

![Padrão de comprometimento típico](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger os seus dispositivos de computação contra esses ataques laterais. A solução isola a gestão e serviços a partir de dispositivos de produtividade menos valiosos, dividir a cadeia antes do dispositivo que tenha acesso a recursos na cloud confidenciais pode ser infiltrated. A solução utiliza serviços nativos do Azure que fazem parte da pilha do Microsoft 365 Enterprise:

* Intune para gestão de dispositivos e uma lista segura de aplicativos e URLs
* Autopilot para a configuração do dispositivo, implantação e atualização
* Azure AD para autenticação multifator, acesso condicional e gestão de utilizadores
* Windows 10 (versão atual) para uma experiência de atestado e o utilizador de estado de funcionamento do dispositivo
* Defender ATP para proteção de ponto de extremidade gerenciado na cloud, a deteção e resposta
* Acesso a recursos de privilegiado do PIM do Azure AD para gerir a autorização e just-in-time (JIT)

## <a name="who-benefits-from-a-secure-workstation"></a>Quem se beneficia de uma estação de trabalho segura?

Todos os utilizadores e operadores se beneficiar ao utilizar uma estação de trabalho segura. Um atacante que compromete um PC ou dispositivo pode representar as contas de contas em cache. Quando iniciar sessão no dispositivo, também poderá utilizar as credenciais e tokens. Este risco faz com que seja importante para proteger os dispositivos que são utilizados para funções com privilégios, incluindo direitos administrativos. Dispositivos com as contas com privilégios são alvos para movimento lateral e ataques de escalamento de privilégio. Estas contas podem ser utilizadas para uma variedade de recursos, tais como:

* Administrador de sistemas baseados em nuvem ou no local
* Estação de trabalho do desenvolvedor de sistemas críticos
* Administrador de conta de redes sociais com exposição elevada
* Estação de trabalho altamente confidencial, tais como um pagamento SWIFT terminal
* Segredos de comerciais de manipulação de estação de trabalho

Para reduzir o risco, deve implementar controlos de segurança elevada para estações de trabalho com privilégios que tornam a utilização destas contas. Para obter mais informações, consulte a [guia de implementação de recursos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plano do Office 365](https://aka.ms/o365secroadmap), e [Roteiro de proteger o acesso privilegiado](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Porquê utilizar estações de trabalho dedicadas?

Embora seja possível adicionar a segurança para um dispositivo existente, é melhor começar com uma base segura. Para colocar a sua organização na melhor posição para manter um nível de alta segurança, comece com um dispositivo que sabe que é seguro e implementar um conjunto de controlos de segurança conhecidos.

Um número crescente de vetores de ataque através de e-mail e navegação na web fica cada vez mais difícil não se esqueça de que um dispositivo pode ser confiável. Este guia pressupõe que uma estação de trabalho dedicada é isolada da produtividade padrão, navegação e e-mail. Remoção de produtividade, a navegação na web e o e-mail de um dispositivo pode ter um impacto negativo na produtividade. No entanto, esta salvaguarda é geralmente aceitável para cenários em que as tarefas de trabalho não exigem explicitamente a ele e o risco de um incidente de segurança é elevado.

> [!NOTE]
> Aqui navegação na Web se refere ao acesso geral para Web sites arbitrários que podem ser uma atividade de alto risco. Tal navegação difere da utilização de um navegador da web para aceder a um pequeno número de sites administrativos bem conhecidos para serviços como o Azure, Office 365, outros fornecedores de serviços cloud e aplicações SaaS.

Estratégias de contenção reforçar a segurança ao aumentar o número e tipo de controles que dissuadir um atacante de obter acesso a ativos confidenciais. O modelo descrito neste artigo usa um design de privilégio em camadas e restringe os privilégios administrativos para dispositivos específicos.

## <a name="supply-chain-management"></a>Gestão de cadeias de fornecimento

Essencial para uma estação de trabalho protegida é uma solução de cadeia de fornecimento em que usa uma estação de trabalho fidedigna chamada raiz da fidedignidade. Para esta solução, utiliza a raiz da fidedignidade [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) tecnologia. Para proteger uma estação de trabalho, o Autopilot permite-lhe tirar partido de dispositivos do Microsoft Windows 10 com otimização de OEM. Estes dispositivos apresentam um bom estado conhecido do fabricante. Em vez de recriar a imagem de um dispositivo potencialmente inseguro, Autopilot pode transformar um dispositivo de Windows num estado "pronto". Aplica-se as definições e políticas, instala aplicações e até mesmo alterar a edição do Windows 10. Por exemplo, Autopilot podem ser alteradas instalação do Windows de um dispositivo do Windows 10 Pro para Windows 10 Enterprise para que ele possa utilizar funcionalidades avançadas.

![Níveis de estação de trabalho seguro](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Funções de dispositivo e de perfis

Esta orientação faz referência a vários perfis de segurança e funções que podem ajudá-lo a criar soluções mais seguras para os utilizadores, os desenvolvedores e pessoal de TI. Estes perfis de equilibrar a facilidade de utilização e os riscos para os utilizadores comuns que podem beneficiar de uma estação de trabalho avançada ou segura. As definições de configurações fornecidas aqui se baseiam em padrões do setor aceites. Esta orientação mostra como proteger o Windows 10 e reduzir os riscos associados com o compromisso de dispositivo ou utilizador. Ela faz isso usando a política e tecnologia para ajudar a gerir os recursos de segurança e riscos.
![Níveis de estação de trabalho seguro](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Segurança baixa** – uma estação de trabalho gerida standard fornece um ponto de partida para a maioria de raiz e de pequena empresa utilização. Estes dispositivos são registados no Azure AD e geridos com o Intune. Este perfil permite aos utilizadores para executar todas as aplicações e procurar qualquer Web site. Como uma solução de antimalware [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve ser ativada.

* **Segurança avançada** – esta solução ao nível da entrada, protegida é bom para os usuários domésticos, usuários de pequenas empresas e desenvolvedores gerais.

   A estação de trabalho avançada é uma forma baseada em políticas para aumentar a segurança do perfil de segurança baixa. Ele fornece um meio seguro para trabalhar com dados de clientes, enquanto também usando ferramentas de produtividade como o e-mail e navegação na web. Pode utilizar o Intune e políticas de auditoria para monitorizar uma estação de trabalho melhorada para utilização de comportamento e perfil de utilizador. Implementar o perfil de estação de trabalho aprimorada com o script do Windows 10 (1809), e tira partido da utilização de proteção de software maligno avançado [proteção avançada de ameaças (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Alta segurança** – a forma mais eficaz para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de self-administer a estação de trabalho. Remover direitos administrativos locais é um passo que melhora a segurança, mas pode afetar produtividade se implementado incorretamente. O perfil de alta segurança baseia-se o perfil de segurança aprimorada com uma alteração considerável: a remoção de administrador local. Este perfil é concebido para utilizadores de perfil alto: executivos, folhas de pagamento e os utilizadores de dados confidenciais, os aprovadores para serviços e processos.

   O usuário de alta segurança exige um ambiente mais controlado enquanto continua a poder fazer atividades como o e-mail e uma experiência simples de usar de navegação na web. Os usuários esperam que os recursos, como cookies, Favoritos e outros atalhos para trabalhar. No entanto, estes utilizadores podem não exigir a capacidade de modificar ou depurar o respetivo dispositivo. Também não precisam instalar controladores. O perfil de alta segurança é implementado com a segurança de alta - script do Windows 10 (1809).

* **Especializada** – os atacantes visam os desenvolvedores e administradores de TI, porque eles podem alterar sistemas de interesse para os invasores. A estação de trabalho especializada expande as políticas da estação de trabalho de alta segurança, gestão de aplicações locais e a limitação de Web sites. Ela também restringe a recursos de produtividade de alto risco, como o ActiveX, Java, plug-ins do browser e outros controles do Windows. Implemente este perfil com DeviceConfiguration_NCSC - script SecurityBaseline do Windows 10 (versão 1803).

* **Protegido** – um atacante que compromete uma conta administrativa pode causar danos de negócio significativas por roubo de dados, alteração de dados ou interrupção do serviço. Neste estado protegida, a estação de trabalho permite que todos os controlos de segurança e políticas que restringem o controle direto do gerenciamento de aplicativos locais. Uma estação de trabalho protegida não tem nenhuma ferramenta de produtividade, por isso, o dispositivo mais difíceis de se comprometer. Bloqueia o vetor mais comuns de ataques de phishing: e-mail e de redes sociais.  A estação de trabalho protegida pode ser implementada com a proteger a estação de trabalho - script SecurityBaseline do Windows 10 (1809).

   ![Estação de trabalho protegida](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece um administrador com uma estação de trabalho protegida que tenha o controlo de aplicações clara e proteção de aplicações. A estação de trabalho utiliza o credential guard, o device guard e exploit guard para proteger o anfitrião de um comportamento malicioso. Todos os discos locais também são encriptados com o BitLocker.

* **Isolado** – este cenário personalizado, offline representa o fim extremo do espectro. Não há scripts de instalação são fornecidos para este caso. Poderá ter de gerir uma função de crítico para a empresa que requer um sistema de operativo herdado não suportado ou sem patch. Por exemplo, uma linha de produção de valor elevado ou um sistema de suporte – vida. Como a segurança é fundamental e não estão disponíveis serviços cloud, pode gerir e atualizar esses computadores manualmente ou com uma arquitetura de floresta do Active Directory isolada, como o Enhanced segurança administrador Environment (ESAE). Nestas circunstâncias, considere remover todos os acessos, exceto as verificações de estado de funcionamento do Intune e o ATP básicas.

  * [Requisito de comunicações de rede do Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Requisito de comunicações de rede do ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passos Seguintes

[Implementar uma estação de trabalho segura geridos pelo Azure](howto-azure-managed-workstation.md).
