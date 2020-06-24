---
title: Acesso Condicional requer dispositivo gerido - Azure Ative Directory
description: Saiba como configurar as políticas de Acesso Condicional do Azure Ative (Azure AD) baseadas em dispositivos que requerem dispositivos geridos para acesso a aplicações na nuvem.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/08/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 690582638451f1691b2ed7a4d0d4d6a7880fd80a
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253210"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: Exigir dispositivos geridos para acesso a aplicações em nuvem com acesso condicional

Num mundo móvel, primeiro em nuvem, o Azure Ative Directory (Azure AD) permite um único acesso a apps e serviços de qualquer lugar. Os utilizadores autorizados podem aceder às suas aplicações na nuvem a partir de uma vasta gama de dispositivos, incluindo dispositivos móveis e também pessoais. No entanto, muitos ambientes possuem pelo menos algumas aplicações que devem ser acedidas apenas por dispositivos que cumprem os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. 

Este artigo explica como pode configurar políticas de Acesso Condicional que requerem dispositivos geridos para aceder a certas aplicações em nuvem no seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

A necessidade de dispositivos geridos para acesso a aplicações na nuvem liga o **Acesso Condicional Azure AD** e a gestão de **dispositivos AZure AD** em conjunto. Se ainda não está familiarizado com uma destas áreas, deve ler os seguintes tópicos, primeiro:

- **[Acesso Condicional no Diretório Ativo Azure](../active-directory-conditional-access-azure-portal.md)** - Este artigo fornece-lhe uma visão geral conceptual do Acesso Condicional e da terminologia relacionada.
- **[Introdução à gestão de dispositivos no Azure Ative Directory](../devices/overview.md)** - Este artigo dá-lhe uma visão geral das várias opções que tem de colocar os dispositivos sob controlo organizacional. 
- Para suporte ao Chrome na **Atualização de Criadores do Windows 10 (versão 1703)** ou posterior, instale a extensão das [contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Esta extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

>[!NOTE] 
> Recomendamos a utilização da política de acesso condicional baseada no dispositivo Azure para obter a melhor aplicação após a autenticação inicial do dispositivo. Isto inclui sessões de encerramento se o dispositivo ficar fora de conformidade e o fluxo de código do dispositivo.


## <a name="scenario-description"></a>Descrição do cenário

Dominar o equilíbrio entre segurança e produtividade é um desafio. A proliferação de dispositivos suportados para aceder aos seus recursos na nuvem ajuda a melhorar a produtividade dos seus utilizadores. Por outro lado, provavelmente não quer que certos recursos no seu ambiente sejam acedidos por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, deve exigir que os utilizadores só possam aceder a eles usando um dispositivo gerido. 

Com o Acesso Condicional Azure AD, pode endereçar este requisito com uma única política que concede acesso:

- Para aplicativos de nuvem selecionados
- Para utilizadores e grupos selecionados
- Requerendo um dispositivo gerido

## <a name="managed-devices"></a>Dispositivos geridos  

Em termos simples, os dispositivos geridos são dispositivos que estão sob *algum tipo* de controlo organizacional. Em Azure AD, o pré-requisito para um dispositivo gerido é que tenha sido registado no Azure AD. Registar um dispositivo cria uma identidade para o dispositivo sob a forma de um objeto do dispositivo. Este objeto é utilizado pela Azure para rastrear informações sobre o estado de utilização de um dispositivo. Como administrador AZure AD, já pode utilizar este objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registado no Azure AD, tem três opções: 

- **Dispositivos registados Azure AD** - para obter um dispositivo pessoal registado no Azure AD
- **A azure AD juntou-se** a dispositivos - para obter um dispositivo organizacional Windows 10 que não está ligado a um AD no local registado no Azure AD. 
- **O Hybrid Azure AD juntou-se** a dispositivos - para obter um Windows 10 ou um dispositivo de nível inferior suportado que se junta a um AD no local registado no AZure AD.

Estas três opções são discutidas no artigo [O que é uma identidade do dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerido, um dispositivo registado deve ser um **dispositivo híbrido Azure AD ou** um dispositivo que tenha sido marcado como **conforme**.  

![Condições baseadas no dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos híbridos Azure AD

Na sua política de Acesso Condicional, pode selecionar **o dispositivo AD Híbrido Azure para** afirmar que as aplicações de nuvem selecionadas só podem ser acedidas através de um dispositivo gerido. 

![Condições baseadas no dispositivo](./media/require-managed-devices/10.png)

Esta definição aplica-se apenas a dispositivos windows 10 ou de nível inferior, como o Windows 7 ou o Windows 8, que se unem a um AD no local. Só é possível registar estes dispositivos com Azure AD utilizando uma junta Híbrida Azure AD, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/45.png)

O que faz de um Dispositivo Híbrido Azure AD um dispositivo gerido?  Para dispositivos que se unem a um AD no local, presume-se que o controlo sobre estes dispositivos é aplicado usando soluções de gestão como **o Gestor de Configuração** ou **a política de grupo (GP)** para os gerir. Uma vez que não existe um método para a Azure AD determinar se algum destes métodos foi aplicado a um dispositivo, exigir um dispositivo híbrido Azure AD é um mecanismo relativamente fraco para exigir um dispositivo gerido. Cabe-lhe a si, como administrador, avaliar se os métodos que são aplicados aos seus dispositivos ligados ao domínio no local são suficientemente fortes para constituir um dispositivo gerido se tal dispositivo for também um dispositivo híbrido Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como conforme

A opção *de exigir que um dispositivo seja marcado como conforme* é a forma mais forte de solicitar um dispositivo gerido.

![Condições baseadas no dispositivo](./media/require-managed-devices/11.png)

Esta opção requer que um dispositivo seja registado no Azure AD, e também para ser marcado como conforme por:
         
- Intune
- Um sistema de gestão de dispositivos móveis de terceiros (MDM) que gere dispositivos Windows 10 através da integração Azure AD. Os sistemas MDM de terceiros para tipos de SISTEMA de dispositivos que não o Windows 10 não são suportados.
 
![Condições baseadas no dispositivo](./media/require-managed-devices/46.png)

Para um dispositivo que está marcado como conforme, pode assumir que: 

- Os dispositivos móveis que a sua força de trabalho utiliza para aceder aos dados da empresa são geridos
- Aplicativos móveis que os seus usos da força de trabalho são geridos
- A informação da sua empresa está protegida ajudando a controlar a forma como a sua força de trabalho acede e partilha
- O dispositivo e as suas aplicações estão em conformidade com os requisitos de segurança da empresa

### <a name="known-behavior"></a>Comportamento conhecido

Ao utilizar o [fluxo OAuth do código do dispositivo,](../develop/v2-oauth2-device-code.md)o controlo de concessão do dispositivo gerido requerido ou a condição do estado do dispositivo não é suportado. Isto porque o dispositivo que executa a autenticação não pode fornecer o estado do seu dispositivo ao dispositivo que fornece um código e o estado do dispositivo no token está bloqueado ao dispositivo que executa a autenticação. Utilize o controlo de concessão de autenticação multi-factor requerendo.

No Windows 7, iOS, Android, macOS e alguns navegadores web de terceiros, a Azure AD identifica o dispositivo usando um certificado de cliente que é a provisionado quando o dispositivo está registado no Azure AD. Quando um utilizador entra pela primeira vez através do navegador, o utilizador é solicitado a selecionar o certificado. O utilizador final deve selecionar este certificado antes de poder continuar a utilizar o navegador.

## <a name="next-steps"></a>Passos seguintes

Antes de configurar uma política de acesso condicional baseada no dispositivo no seu ambiente, deve dar uma olhada nas [melhores práticas de acesso condicional no Diretório Ativo Azure](best-practices.md).
