---
title: Como-exigir dispositivos para aceder à aplicação de cloud com o acesso condicional do Azure Active Directory geridos | Documentos da Microsoft
description: Saiba como configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory (Azure AD) que necessitam de dispositivos geridos para aceder à aplicação de cloud.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c468f149a8005f2452ba49afbb447c3304c3d4b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170535"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: Exigir que os dispositivos geridos para aceder à aplicação de cloud com o acesso condicional

Num mundo de dispositivos móveis e da cloud, Azure Active Directory (Azure AD) permite início de sessão único para aplicações e serviços de qualquer lugar. Usuários autorizados possam acessar suas aplicações na cloud a partir de uma ampla gama de dispositivos, incluindo móveis e também dispositivos pessoais. No entanto, muitos ambientes têm, pelo menos, algumas aplicações que devem ser acessadas somente por dispositivos que cumprem as normas de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. 

Este artigo explica como pode configurar políticas de acesso condicional que exigem a dispositivos geridos para aceder a determinadas aplicações na cloud no seu ambiente. 


## <a name="prerequisites"></a>Pré-requisitos

Exigir que os dispositivos geridos para ligações de acesso de aplicação na cloud **acesso condicional do Azure AD** e **gestão de dispositivos do Azure AD** em conjunto. Se ainda não está familiarizados com uma dessas áreas, leia os tópicos seguintes, pela primeira vez:

- **[Acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md)**  -este artigo fornece uma descrição geral conceptual de acesso condicional e a terminologia relacionada.

- **[Introdução à gestão de dispositivos no Azure Active Directory](../devices/overview.md)**  -este artigo dá-lhe uma descrição geral das várias opções, terá de os dispositivos sob controle organizacional. 


## <a name="scenario-description"></a>Descrição do cenário

Dominar o equilíbrio entre segurança e produtividade é um desafio. A proliferação de dispositivos suportados para aceder aos recursos da cloud ajuda a aumentar a produtividade dos seus utilizadores. Por outro lado, provavelmente não quer determinados recursos no seu ambiente para ser acedida por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, deve exigir que os utilizadores só podem acedê-los usando um dispositivo gerido. 

Com acesso condicional do Azure AD, pode solucionar esse requisito com uma única política que concede acesso:

- Para aplicações na cloud selecionada

- Para utilizadores e grupos selecionados

- Exigir um dispositivo gerido


## <a name="managed-devices"></a>Dispositivos geridos  

Em termos simples, os dispositivos geridos são dispositivos que estão sob *algum tipo* do controle organizacional. No Azure AD, o pré-requisito para um dispositivo gerido é que ele foi registado com o Azure AD. Registar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Este objeto é utilizado pelo Azure para controlar informações de estado sobre um dispositivo. Como administrador do Azure AD, já pode utilizar este objeto para ativar/desativar (Ativar/desativar) o estado de um dispositivo.
  
![Condições com base no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registado com o Azure AD, tem três opções:

- **[Dispositivos registados do Azure AD](../devices/overview.md#azure-ad-registered-devices)**  - para obter um dispositivo pessoal registado no Azure AD

- **[Dispositivos associados ao Azure AD](../devices/overview.md#azure-ad-joined-devices)**  - para fazer com que um dispositivo Windows 10 organizacional que não está associado com um local do AD registado com o Azure AD. 

- **[Dispositivos associados ao Azure AD híbrido](../devices/overview.md#hybrid-azure-ad-joined-devices)**  - para obter um Windows 10 ou dispositivo suportado de nível inferior que esteja associado a uma local do AD registado com o Azure AD.

Para se tornar um dispositivo gerido, um dispositivo registado tem de ser um **dispositivo associado ao Azure AD híbrido** ou uma **dispositivo que foi marcado como compatível**.  

![Condições com base no dispositivo](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Exigir Hybrid Azure AD de dispositivos associados

Na sua política de acesso condicional, pode selecionar **requerer dispositivo associado do Azure AD híbrido** para o estado de que as aplicações na cloud selecionada só podem ser acessadas através de um dispositivo gerido. 

![Condições com base no dispositivo](./media/require-managed-devices/10.png)

Esta definição aplica-se apenas ao Windows 10 ou dispositivos de nível inferior, como o Windows 7 ou Windows 8, que estão associados com um local do AD. Apenas pode registar estes dispositivos com o Azure AD com uma associação Azure AD híbrido, que é um [processo](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registado. 

![Condições com base no dispositivo](./media/require-managed-devices/45.png)

O que torna um Azure AD híbrido associado a um dispositivo um dispositivo gerido?  Para dispositivos que estão associados a uma local do AD, é assumido que o controle sobre esses dispositivos é aplicado com soluções de gestão, como **System Center Configuration Manager (SCCM)** ou **(GP)depolíticadegrupo** geri-los. Porque não existe nenhum método para o Azure AD determinar se qualquer um destes métodos foi aplicada a um dispositivo, que requerem um dispositivo associado ao Azure AD híbrido é um mecanismo relativamente fraco para exigir um dispositivo gerido. Cabe a como administrador para avaliar se os métodos que são aplicados para o seu local associados a um domínio dispositivos estão fortes o suficiente para que constitua um dispositivo gerido se tais um dispositivo também é um dispositivo associado ao Azure AD híbrido.


## <a name="require-device-to-be-marked-as-compliant"></a>Pedir que o dispositivo seja marcado como compatível

A opção para *requer um dispositivo ser marcado como compatível* é a maneira mais forte para pedir um dispositivo gerido.

![Condições com base no dispositivo](./media/require-managed-devices/11.png)

Esta opção requer um dispositivo ser registado com o Azure AD bem como para ser marcado como conforme por:
         
- Intune.
- Um sistema de gestão (MDM) de dispositivos móveis de terceiros que gere os dispositivos Windows 10 por meio da integração do Azure AD. Sistemas MDM de terceiros para tipos de SO do dispositivo que não seja o Windows 10 não são suportados.
 
![Condições com base no dispositivo](./media/require-managed-devices/46.png)



Para um dispositivo que está marcado como compatível, pode assumir que: 

- Os dispositivos móveis que sua força de trabalho utiliza para aceder aos dados da empresa são geridos
- Utiliza a sua força de trabalho de aplicações móveis são geridas
- Informações da sua empresa estão protegidas por ajudar a controlar a forma como sua força de trabalho acede e partilhe o mesmo
- O dispositivo e as suas aplicações estão em conformidade com requisitos de segurança da empresa




## <a name="next-steps"></a>Passos Seguintes

Antes de configurar uma política de acesso condicional com base no dispositivo no seu ambiente, deve dar uma olhada a [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md).

