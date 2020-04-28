---
title: Acesso Condicional requer dispositivo gerido - Diretório Ativo Azure
description: Saiba como configurar as políticas de Acesso Condicional baseadas em dispositivos Azure Ative (Azure AD) que requerem dispositivos geridos para acesso a aplicações na nuvem.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481488"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: Exigir dispositivos geridos para acesso a aplicações na nuvem com acesso condicional

Num mundo mobile-first, em primeira nuvem, o Azure Ative Directory (Azure AD) permite um único início de inscrição em apps e serviços de qualquer lugar. Os utilizadores autorizados podem aceder às suas aplicações na nuvem a partir de uma ampla gama de dispositivos, incluindo dispositivos móveis e também pessoais. No entanto, muitos ambientes têm pelo menos algumas aplicações que só devem ser acedidas por dispositivos que cumprem os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. 

Este artigo explica como pode configurar políticas de Acesso Condicional que requerem dispositivos geridos para aceder a determinadas aplicações na nuvem no seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Requerendo dispositivos geridos para acesso a aplicações na nuvem liga o **Acesso Condicional Azure AD** e a gestão de **dispositivos Azure AD** em conjunto. Se ainda não está familiarizado com uma destas áreas, deve ler os seguintes tópicos, primeiro:

- **[Acesso Condicional no Diretório Ativo Azure](../active-directory-conditional-access-azure-portal.md)** - Este artigo fornece-lhe uma visão geral conceptual do Acesso Condicional e da terminologia relacionada.
- **[Introdução à gestão de dispositivos no Diretório Ativo do Azure](../devices/overview.md)** - Este artigo dá-lhe uma visão geral das várias opções que tem para obter dispositivos sob controlo organizacional. 
- Para suporte chrome na **Atualização de Criadores do Windows 10 (versão 1703)** ou posterior, instale a extensão das [Contas Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Esta extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

>[!NOTE] 
> Recomendamos a utilização da política de Acesso Condicional baseada em dispositivos Azure AD para obter a melhor aplicação após a autenticação inicial do dispositivo. Isto inclui sessões de fecho se o dispositivo cair fora de conformidade e fluxo de código do dispositivo.


## <a name="scenario-description"></a>Descrição do cenário

Dominar o equilíbrio entre segurança e produtividade é um desafio. A proliferação de dispositivos suportados para aceder aos seus recursos na nuvem ajuda a melhorar a produtividade dos seus utilizadores. Por outro lado, provavelmente não quer que certos recursos no seu ambiente sejam acedidos por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, deve exigir que os utilizadores só possam aceder a eles através de um dispositivo gerido. 

Com o Acesso Condicional Azure AD, pode abordar este requisito com uma única política que concede acesso:

- Para aplicativos de nuvem selecionados
- Para utilizadores e grupos selecionados
- Exigindo um dispositivo gerido

## <a name="managed-devices"></a>Dispositivos geridos  

Em termos simples, os dispositivos geridos são dispositivos que estão sob *algum tipo* de controlo organizacional. Em Azure AD, o pré-requisito para um dispositivo gerido é que foi registado na Azure AD. O registo de um dispositivo cria uma identidade para o dispositivo sob a forma de um objeto de dispositivo. Este objeto é utilizado pelo Azure para rastrear informações sobre o estado de um dispositivo. Como administrador da AD Azure, já pode utilizar este objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas em dispositivos](./media/require-managed-devices/32.png)

Para obter um dispositivo registado na Azure AD, tem três opções: 

- **Dispositivos registados pela Azure AD** - para obter um dispositivo pessoal registado na Azure AD
- **A Azure AD juntou-se** a dispositivos - para obter um dispositivo organizacional do Windows 10 que não esteja ligado a um AD no local registado com a AD Azure. 
- **A Hybrid Azure AD juntou-se** a dispositivos - para obter um Windows 10 ou um dispositivo de nível inferior suportado que esteja ligado a um AD no local registado com AD Azure.

Estas três opções são discutidas no artigo O que é uma identidade de [dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerido, um dispositivo registado deve ser um **dispositivo híbrido Azure AD ou** um dispositivo que tenha sido marcado como **conforme**.  

![Condições baseadas em dispositivos](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Exigir dispositivos ad ad híbridos Azure

Na sua política de Acesso Condicional, pode selecionar **o Dispositivo AD Hybrid Azure** para afirmar que as aplicações em nuvem selecionadas só podem ser acedidas através de um dispositivo gerido. 

![Condições baseadas em dispositivos](./media/require-managed-devices/10.png)

Esta definição aplica-se apenas ao Windows 10 ou a dispositivos de nível inferior, como o Windows 7 ou Windows 8, que se juntam a um AD no local. Só é possível registar estes dispositivos com a AD Azure utilizando uma adesão híbrida azure AD, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para registar um dispositivo Windows 10. 

![Condições baseadas em dispositivos](./media/require-managed-devices/45.png)

O que faz de um Hybrid Azure AD um dispositivo gerido?  Para dispositivos que se juntam a um AD no local, presume-se que o controlo sobre estes dispositivos é aplicado utilizando soluções de gestão como o Gestor de **Configuração** ou a política de **grupo (GP)** para os gerir. Como não existe um método para a AD Azure determinar se algum destes métodos foi aplicado a um dispositivo, exigir um dispositivo híbrido Azure AD é um mecanismo relativamente fraco para exigir um dispositivo gerido. Cabe-lhe, enquanto administrador, avaliar se os métodos que são aplicados aos seus dispositivos ligados ao domínio no local são suficientemente fortes para constituir um dispositivo gerido se tal dispositivo for também um dispositivo híbrido Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como conforme

A opção *de exigir que um dispositivo seja marcado como conforme* é o formulário mais forte para solicitar um dispositivo gerido.

![Condições baseadas em dispositivos](./media/require-managed-devices/11.png)

Esta opção requer que um dispositivo seja registado com a Azure AD, e também seja marcado como conforme por:
         
- Intune
- Um sistema de gestão de dispositivos móveis (MDM) de terceiros que gere dispositivos Windows 10 através da integração da AD Azure. Os sistemas de MDM de terceiros para tipos de SISTEMA de dispositivos que não o Windows 10 não são suportados.
 
![Condições baseadas em dispositivos](./media/require-managed-devices/46.png)

Para um dispositivo que está marcado como conforme, pode presumir que: 

- Os dispositivos móveis que a sua força de trabalho usa para aceder aos dados da empresa são geridos
- Aplicações móveis que a sua força de trabalho usa são geridas
- A informação da sua empresa está protegida ajudando a controlar a forma como a sua força de trabalho acede e partilha
- O dispositivo e as suas aplicações estão em conformidade com os requisitos de segurança da empresa

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores web de terceiros A AD AD identifica o dispositivo utilizando um certificado de cliente que é aprovisionado quando o dispositivo está registado na Azure AD. Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador final deve selecionar este certificado antes de poder continuar a utilizar o navegador.

## <a name="next-steps"></a>Passos seguintes

Antes de configurar uma política de acesso condicional baseada em dispositivos no seu ambiente, deve analisar as [melhores práticas de acesso condicional no Diretório Ativo Azure](best-practices.md).
