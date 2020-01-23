---
title: O acesso condicional requer dispositivo gerenciado-Azure Active Directory
description: Saiba como configurar políticas de acesso condicional com base no dispositivo Azure Active Directory (Azure AD) que exigem dispositivos gerenciados para acesso ao aplicativo de nuvem.
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
ms.openlocfilehash: bb0764b9c2c43faf88db165a11ae963c4f170f01
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512594"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional

Em um mundo móvel e em nuvem, o Azure Active Directory (Azure AD) habilita o logon único para aplicativos e serviços de qualquer lugar. Os usuários autorizados podem acessar seus aplicativos de nuvem a partir de uma ampla variedade de dispositivos, incluindo dispositivos móveis e também pessoais. No entanto, muitos ambientes têm pelo menos alguns aplicativos que só devem ser acessados por dispositivos que atendam aos seus padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. 

Este artigo explica como você pode configurar políticas de acesso condicional que exigem que dispositivos gerenciados acessem determinados aplicativos de nuvem em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Exigir dispositivos gerenciados para acesso ao aplicativo de nuvem vincula o **acesso condicional do Azure ad** e o **Gerenciamento de dispositivos do Azure ad** juntos. Se você ainda não estiver familiarizado com uma dessas áreas, leia os tópicos a seguir, primeiro:

- **[Acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** -este artigo fornece uma visão geral conceitual do acesso condicional e da terminologia relacionada.
- **[Introdução ao gerenciamento de dispositivos no Azure Active Directory](../devices/overview.md)** -este artigo fornece uma visão geral das várias opções que você tem para obter dispositivos sob controle organizacional. 

## <a name="scenario-description"></a>Descrição do cenário

Dominar o equilíbrio entre segurança e produtividade é um desafio. A proliferação de dispositivos com suporte para acessar seus recursos de nuvem ajuda a melhorar a produtividade de seus usuários. No lado do inverso, você provavelmente não quer que determinados recursos em seu ambiente sejam acessados por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários só possam acessá-los usando um dispositivo gerenciado. 

Com o acesso condicional do Azure AD, você pode atender a esse requisito com uma única política que concede acesso:

- Para aplicativos de nuvem selecionados
- Para usuários e grupos selecionados
- Exigindo um dispositivo gerenciado

## <a name="managed-devices"></a>Dispositivos geridos  

Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Azure AD, o pré-requisito para um dispositivo gerenciado é que ele foi registrado no Azure AD. O registro de um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para acompanhar informações de status sobre um dispositivo. Como administrador do Azure AD, você já pode usar esse objeto para ativar/desativar (habilitar/desabilitar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registrado com o Azure AD, você tem três opções: 

- **Dispositivos registrados no Azure ad** – para obter um dispositivo pessoal registrado no Azure AD
- **Dispositivos ingressados no Azure ad** – para obter um dispositivo organizacional Windows 10 que não está associado a um AD local registrado com o Azure AD. 
- **Dispositivos ingressados no Azure ad híbrido** -para obter um dispositivo de nível inferior do Windows 10 ou com suporte que tenha ingressado em um AD local registrado com o Azure AD.

Essas três opções são discutidas no artigo [o que é uma identidade de dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerenciado, um dispositivo registrado deve ser um **dispositivo ingressado no Azure ad híbrido** ou um **dispositivo que foi marcado como compatível**.  

![Condições baseadas no dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Exigir dispositivos ingressados no Azure AD híbrido

Em sua política de acesso condicional, você pode selecionar **exigir dispositivo ingressado no Azure ad híbrido** para declarar que os aplicativos de nuvem selecionados só podem ser acessados usando um dispositivo gerenciado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/10.png)

Essa configuração se aplica somente a dispositivos Windows 10 ou de nível inferior, como o Windows 7 ou o Windows 8, que fazem parte de um AD local. Você só pode registrar esses dispositivos com o Azure AD usando uma junção híbrida do Azure AD, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registrado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/45.png)

O que torna um dispositivo associado do Azure AD híbrido um dispositivo gerenciado?  Para dispositivos que ingressaram em um AD local, supõe-se que o controle desses dispositivos seja imposto usando soluções de gerenciamento, como **Configuration Manager** ou **GP (política de grupo)** para gerenciá-los. Como não há nenhum método para o Azure AD determinar se algum desses métodos foi aplicado a um dispositivo, exigir um dispositivo ingressado no Azure AD híbrido é um mecanismo relativamente fraco para exigir um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos aplicados aos seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado se esse dispositivo também for um dispositivo ingressado no Azure AD híbrido.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

A opção de *exigir que um dispositivo seja marcado como em conformidade* é a forma mais forte de solicitar um dispositivo gerenciado.

![Condições baseadas no dispositivo](./media/require-managed-devices/11.png)

Essa opção requer que um dispositivo seja registrado no Azure AD e também seja marcado como compatível pelo:
         
- Intune
- Um sistema de MDM (gerenciamento de dispositivo móvel) de terceiros que gerencia dispositivos Windows 10 por meio da integração do Azure AD. Não há suporte para sistemas MDM de terceiros para tipos de so do dispositivo que não sejam o Windows 10.
 
![Condições baseadas no dispositivo](./media/require-managed-devices/46.png)

Para um dispositivo marcado como compatível, você pode pressupor que: 

- Os dispositivos móveis que sua força de funcionários usa para acessar os dados da empresa são gerenciados
- Aplicativos móveis que seus funcionários usam são gerenciados
- As informações da sua empresa são protegidas ajudando-o a controlar a maneira como sua força de seus acessa e compartilha
- O dispositivo e seus aplicativos estão em conformidade com os requisitos de segurança da empresa

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores da Web de terceiros, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário final deve selecionar esse certificado antes que possa continuar a usar o navegador.

## <a name="next-steps"></a>Passos seguintes

Antes de configurar uma política de acesso condicional com base no dispositivo em seu ambiente, você deve examinar as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
