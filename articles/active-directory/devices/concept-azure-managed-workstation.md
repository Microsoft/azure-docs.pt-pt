---
title: Por isso que as estações de trabalho seguras são importantes - Azure Active Directory
description: Saiba por que as organizações a criar estações de trabalho de geridos pelo Azure seguras
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
ms.openlocfilehash: f3e33e483bdc9440ced1a2bc8aec6f21272eb797
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052169"
---
# <a name="building-secure-workstations"></a>Criação de estações de trabalho seguras

Estações de trabalho isoladas seguras são extremamente importantes para garantir a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Muitos outros controlos de segurança e garantias irão falhar ou não têm qualquer efeito se a segurança de estação de trabalho de cliente subjacente foi comprometida.

Este documento explica o que é necessário para criar uma estação de trabalho de cliente seguras com instruções passo a passo detalhadas, incluindo como configurar a partir de controlos de segurança. Este tipo de estações de trabalho às vezes é chamado de uma estação de trabalho acesso privilegiado (PAW), que esta referência é utilizada e construída. No entanto, a documentação de orientação é a tecnologia baseada na nuvem para gerir o serviço e apresenta os recursos de segurança introduzidos a partir de Windows 10RS5, o Microsoft Defender ATP, o Azure Active Directory e o Intune.

> [!NOTE]
> Este artigo explica o conceito de estação de trabalho segura e sua importância. Se já estiver familiarizado com o conceito e gostaria de ignorar a implementação, leia [como implementar uma estação de trabalho seguro](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-securing-workstation-access-is-important"></a>Por que é importante proteger o acesso de estação de trabalho

A rápida adoção de serviços cloud e a capacidade de trabalhar a partir de qualquer local tiver criado um novo método para exploração. Os atacantes estão a explorar os controles de segurança frágeis em dispositivos em que os administradores funcionarem e conseguem obter acesso a recursos com privilégios.

Conforme documentado no [relatório de ameaças da Verizon](https://enterprise.verizon.com/resources/reports/dbir/), e [relatório de inteligência de segurança](https://aka.ms/sir) uso indevido de privilégios e ataques de cadeia de fornecimento estão entre os mecanismos de cinco principais usados para violar a organizações e o em segundo lugar mais detectada o tática em incidentes reportados em 2018.

A maioria dos invasores seguem o caminho abaixo:

* Começar com o reconhecimento, muitas vezes específico da indústria, para encontrar um caminho para
* Analisar as informações recolhidas para identificar o melhor meio para obter acesso (infiltração) de uma estação de trabalho de baixa de valor percebido
* Persistência e veja significa mover [-se lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Infiltrar confidenciais e dados

Os atacantes frequentemente se infiltrarem em dispositivos que parecem baixo risco ou undervalued para reconhecimento. Estes dispositivos vulneráveis, em seguida, são utilizados para localizar uma oportunidade para movimento lateral, encontrar dispositivos e utilizadores administrativos e identificar os dados importantes elevados, a com êxito retirar informações depois de obterem estas funções de utilizador com privilégios.

![Padrão de comprometimento típico](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento fornece uma solução para o ajudar a proteger os seus dispositivos de computação através do isolamento de gestão e serviços para ajudar a proteger contra o movimento lateral ou ataques dos dispositivos de produtividade menos valiosos. O design ajuda a reduzir a capacidade de executar com êxito uma violação ao dividir a cadeia antes de infiltração do dispositivo utilizado para gerir ou aceder aos recursos da cloud confidenciais. A solução descrita passarão a utilizar os serviços do Azure nativos que fazem parte do pilha do Microsoft 365 Enterprise, incluindo:

* Intune para gestão de dispositivos, incluindo a aplicação e listas de permissões de URL
* Autopilot para a configuração do dispositivo e implantação e atualização 
* Azure AD para autenticação multifator, acesso condicional e gestão de utilizadores
* Windows 10 (versão atual) para uma experiência de atestado e o utilizador de estado de funcionamento do dispositivo
* Microsoft Defender avançadas de proteção contra ameaças (ATP) para o endpoint protection, deteção e resposta com a gestão da cloud
* Acesso aos recursos de privilegiado do PIM do Azure AD para gerir a autorização, incluindo apenas no Time (JIT)

## <a name="who-benefit-from-using-a-secure-workstation"></a>Quem beneficiar da utilização de uma estação de trabalho segura

Todos os utilizadores e operadores beneficiam da utilização de uma estação de trabalho segura. Um atacante que compromete um PC ou dispositivo pode fazer várias coisas, inclusive representar todas as contas em cache e utilizar as credenciais e tokens utilizados nesse dispositivo, apesar de terem feito logon. Este risco torna a proteger os dispositivos utilizados para qualquer função com privilégios, incluindo direitos administrativos tão importantes como dispositivos em que é utilizada uma conta com privilégios estão destinos de movimento lateral e ataques de escalamento de privilégio. Estas contas podem ser utilizadas para uma variedade de recursos, tais como:

* Administradores de sistemas baseados em nuvem e no local
* Estações de trabalho do desenvolvedor de sistemas críticos
* Administrador de contas de redes sociais com exposição elevada
* Estações de trabalho altamente confidenciais, como terminais de pagamento SWIFT
* Estações de trabalho de processamento de segredos comerciais

A Microsoft recomenda a implementação de controlos de segurança elevadas para estações de trabalho com privilégios em que essas contas são usadas para reduzir o risco. Orientações adicionais podem ser encontrada na [guia de implementação de recursos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [plano do Office 365](https://aka.ms/o365secroadmap), e [Roteiro de proteger o acesso privilegiado](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Por que motivo dedicado estações de trabalho

Embora seja possível adicionar a segurança para um dispositivo existente, é melhor começar com uma base segura. Começando com um dispositivo de bom conhecido e um conjunto de segurança conhecidos controles coloca sua organização, na melhor posição para manter o que aumenta o nível de segurança. Com o número cada vez maior de vetores de ataque permitido pelo e-mail casual e navegação na web, é cada vez mais difícil garantir que um dispositivo pode ser confiável. Isso funciona guia sob a suposição de uma estação de trabalho dedicada separado do padrão produtividade, navegação, e tarefas de e-mail são concluídas. Remoção de produtividade, a navegação na web e o e-mail de um dispositivo pode ter um impacto negativo na produtividade, mas esta salvaguarda é geralmente aceitável para cenários em que as tarefas de trabalho não exigem explicitamente a ele e o risco de um incidente de segurança é elevado.

> [!NOTE]
> Aqui navegação na Web se refere a acesso geral para os Web sites arbitrários, que é um risco elevado distintos da utilização de um navegador da web para aceder a um pequeno número de sites administrativos bem conhecidos para serviços como o Azure, Office 365, outros fornecedores de serviços cloud e SaaS aplicações.

Estratégias de contenção fornecem garantias de segurança aumentada ao aumentar o número e tipo de controlos de que um adversário precisa superar para poder aceder a ativos confidenciais. O modelo de desenvolvido aqui fornece a contenção de privilégios administrativos para dispositivos específicos usando um modelo de privilégios em camadas.

## <a name="supply-chain-management"></a>Gestão de cadeias de fornecimento

Essencial para uma estação de trabalho protegida é uma solução de cadeia de fornecimento em que a estação de trabalho que utiliza é confiável, "raiz de confiança". Esta solução irão abordar a raiz de confiança utilizando o [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) tecnologia. Para uma estação de trabalho protegida Microsoft Autopilot fornece a capacidade de usar dispositivos Microsoft com otimização de OEM Windows 10 que fornecem um bom estado conhecido do fabricante. Em vez de recriar a imagem de um dispositivo que poderá não ser fidedigno, Microsoft Autopilot podem transformar um dispositivo de Windows num estado "pronto", aplicar as definições e políticas, a instalação de aplicações, e até mesmo alterar a edição do Windows 10 a ser utilizadas (por exemplo, a partir de Windows 10 Pro para do Windows 10 Enterprise, para oferecer suporte a recursos avançados).

![Níveis de estação de trabalho seguro](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Funções de dispositivo e de perfis

Em toda a documentação de orientação, vários perfis de segurança e as funções serão resolvidas para obter uma solução mais segura para os utilizadores, os desenvolvedores e equipe de operações de TI. Estes perfis foi alinhados para oferecer suporte a usuários comuns em organizações que podem beneficiar de uma estação de trabalho avançada ou segura, ao balanceamento de usabilidade e risco. A documentação de orientação irá fornecer a configuração das definições com base nos padrões do setor aceites. Esta orientação é utilizada para ilustrar um método no sistema de proteção com o Windows 10 e reduzir os riscos associados com o compromisso de dispositivo ou utilizador usando a política e tecnologia para ajudar a gerir os recursos de segurança e riscos.
![Níveis de estação de trabalho seguro](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Segurança baixa** – uma estação de trabalho gerida standard fornece um ponto de partida para a maioria de raiz e de pequena empresa utilização. Estes dispositivos são do Azure AD registado e geridos pelo Intune. O perfil permite aos utilizadores para executar todas as aplicações e procurar qualquer Web site. Como uma solução antimalware [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve ser ativada.
* **Segurança avançada** – é uma solução de nível de entrada protegido, bem para usuários domésticos, usuários de pequenas empresas, bem como desenvolvedores gerais.
   * A estação de trabalho avançado fornece uma política baseada significa para melhorar a segurança do perfil de segurança baixa. Este perfil permite que um meio seguro para trabalhar com dados dos clientes e conseguir utilizar ferramentas de produtividade, como a verificação de e-mail e navegação na web. Uma estação de trabalho avançado pode ser utilizada para auditar o comportamento do utilizador e a utilização de perfil de uma estação de trabalho ao ativar políticas de auditoria e Registro em log para o Intune. Neste perfil, a estação de trabalho irá ativar os controlos de segurança e políticas descritas no conteúdo e implementado no avançada estação de trabalho - Windows 10 (1809) de script. A implementação também tira partido da utilização de proteção de software maligno avançado [proteção avançada de ameaças (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Alta segurança** – a forma mais eficaz para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de self-administer a estação de trabalho. Remover direitos administrativos locais é uma etapa que melhora a segurança e pode afetar a produtividade se implementado incorretamente. O perfil de alta segurança baseia-se o perfil de segurança aprimorada com uma alteração considerável, a remoção de administrador local. Este perfil foi concebido para ajudar com os utilizadores que talvez um utilizador de perfil alto, como um executivo ou utilizadores que possam ter contato com dados confidenciais, como a folha de pagamento ou aprovação de serviços e processos.
   * O perfil de usuário de alta segurança exige um ambiente controlado superior enquanto continua a poder realizar suas atividades de produtividade, como o correio e mantendo uma simples para utilizar a experiência de navegação na web. Os usuários esperam que os recursos, como cookies, Favoritos e outros atalhos disponíveis para operar. No entanto estes utilizadores podem não exigir a capacidade de modificar ou depurar o respetivo dispositivo e não serão necessário instalar controladores. O perfil de alta segurança é implementado com a segurança de alta - script do Windows 10 (1809).
* **Especializada** – os desenvolvedores e administradores de TI são um alvo atraente para os atacantes, como estas funções podem alterar sistemas de interesse para os invasores. A estação de trabalho especializada leva o esforço implementado na estação de trabalho de alta segurança e emphases ainda mais sua segurança, na gestão de aplicações locais, limitando os web sites da internet e restringir as capacidades de produtividade de alta risco, como o ActiveX, Java, do navegador Plug-in e vários outros controles conhecidos de alto risco num dispositivo Windows. Neste perfil, a estação de trabalho irá ativar os controlos de segurança e as políticas descritas no conteúdo e implementado no DeviceConfiguration_NCSC - Windows 10 (versão 1803) SecurityBaseline script.
* **Protegido** – um invasor que pode comprometer a uma conta administrativa normalmente pode causar danos de negócio significativas por roubo de dados, alteração de dados ou interrupção do serviço. Neste estado protegida, a estação de trabalho permitirá que todos os controlos de segurança e políticas que restringem o controle direto de gestão de aplicações locais e ferramentas de produtividade são removidas. Como resultado, comprometer o dispositivo fica mais difícil como email e de redes sociais está bloqueados que refletem a forma mais comum, ataques de phishing podem ter êxito.  A estação de trabalho protegida pode ser implementada com a proteger a estação de trabalho - script SecurityBaseline do Windows 10 (1809).

   ![Estação de trabalho protegida](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece ao administrador uma estação de trabalho protegida que tenha o controlo de aplicativo evidentes e a proteção de aplicações. A estação de trabalho irá utilizar credencial, o dispositivo e o exploit guard para proteger o anfitrião de um comportamento malicioso. Além disso, todos os locais discos são encriptados com a encriptação de disco Bitlocker.

* **Isolado** – este cenário offline personalizado representa o fim extremo do espectro (nenhum script de instalação é fornecido para este caso). As organizações poderão ter de gerir uma negócio isolado função crítica, como uma linha de produção de valor elevado ou sistemas de suporte de vida que necessita não suportado/sem patch sistemas operacionais herdados. Como a segurança é fundamental e não estão disponíveis serviços cloud, as organizações podem manualmente gerir/atualizar esses computadores ou utilizar uma arquitetura de floresta do Active Directory (como o Enhanced Security administrador Environment (ESAE)) isolada para geri-los. Estes circunstância remover todo o acesso, exceto básica Intune e o ATP deve ser considerada a verificar o estado de funcionamento.
   * [Requisito de comunicações de rede do Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [Requisito de comunicações de rede do ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Passos Seguintes

[Implementação de uma estação de trabalho segura geridos pelo Azure](howto-azure-managed-workstation.md)
