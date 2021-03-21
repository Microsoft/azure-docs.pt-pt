---
title: Políticas de acesso condicional migração - Diretório Ativo Azure
description: Saiba o que precisa de saber para migrar políticas clássicas no portal Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601661"
---
# <a name="conditional-access-classic-policy-migration"></a>Migração clássica de política de acesso condicional

O Acesso Condicional é a ferramenta utilizada pelo Azure Ative Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O Acesso Condicional está no centro do novo plano de controlo de identidade. Embora o objetivo ainda seja o mesmo, o lançamento do novo portal Azure introduziu melhorias significativas no funcionamento do Acesso Condicional.

Considere migrar as políticas que não criou no portal Azure porque:

- Agora pode abordar cenários que não conseguiu lidar antes.
- Pode reduzir o número de políticas que tem de gerir consolidando-as.
- Pode gerir todas as suas políticas de Acesso Condicional num local central.
- O portal clássico do Azure será retirado.

Este artigo explica o que precisa de saber para migrar as suas políticas de Acesso Condicional existentes para o novo quadro.

## <a name="classic-policies"></a>Políticas clássicas

No [portal Azure,](https://portal.azure.com)as políticas de acesso condicional podem ser encontradas no âmbito do Acesso Condicional de Segurança **do Diretório Ativo Azure.**  >    >   A sua organização também pode ter políticas de Acesso Condicional mais antigas não criadas usando esta página. Estas políticas são conhecidas como *políticas clássicas.* As políticas clássicas são políticas de acesso condicional, que criou em:

- O portal clássico Azure
- O portal clássico intune
- O portal de proteção de aplicações Intune

Na página **Acesso Condicional,** pode aceder às suas políticas clássicas clicando em [**políticas clássicas**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na secção **Gerir.** 

![Acesso Condicional em AD Azure mostrando visão de políticas clássicas](./media/policy-migration/71.png)

A visão **de políticas** clássicas oferece-lhe uma opção para:

- Filtre as suas políticas clássicas.
- Desativar as políticas clássicas.
- Reveja as definições de uma política clássica e desative-a.

   ![Detalhes de política clássicos, incluindo a configuração de política existente](./media/policy-migration/74.png)

> [!WARNING]
> Uma vez desativada, uma política clássica não pode ser reativada.

A visão de detalhes de uma política clássica permite-lhe documentar as definições, modificar os grupos incluídos ou excluídos e desativar a política.

![Detalhes da política - Grupos para incluir ou excluir](./media/policy-migration/75.png)

Alterando os grupos selecionados ou excluindo grupos específicos, pode testar o efeito de uma política clássica desativada para alguns utilizadores de teste antes de desativar a política para todos os utilizadores e grupos incluídos.
 
## <a name="migration-considerations"></a>Migration considerations (Considerações sobre a migração)

Neste artigo, as políticas de acesso condicionado Azure AD são também referidas como *novas políticas.*
As suas políticas clássicas continuam a trabalhar lado a lado com as suas novas políticas até que as desative ou elimine. 

Os seguintes aspetos são importantes no contexto de uma consolidação das políticas:

- Enquanto as políticas clássicas estão ligadas a uma aplicação específica na nuvem, pode selecionar quantas aplicações em nuvem precisar de uma nova política.
- Os controlos de uma política clássica e de uma nova política para uma aplicação em nuvem exigem que todos os controlos *(E)* sejam cumpridos. 
- Numa nova política, pode:
   - Combine várias condições, se necessário pelo seu cenário. 
   - Selecione vários requisitos de concessão como controlo de acesso e combine-os com um *OR* lógico (requerer um dos controlos selecionados) ou com um *E* lógico (requere todos os controlos selecionados).

### <a name="exchange-online"></a>Exchange online

Se quiser migrar políticas clássicas para **o Exchange online** que incluam o Exchange Ative **Sync** como condição de aplicações de clientes, poderá não conseguir consolidá-las numa nova política. 

Este é, por exemplo, o caso se quiser suportar todos os tipos de aplicações do cliente. Numa nova política que tem **o Exchange Ative Sync** como condição de aplicações de clientes, não é possível selecionar outras aplicações de clientes.

![Acesso Condicional selecionando aplicativos de clientes](./media/policy-migration/64.png)

Uma consolidação numa nova política também não é possível se as suas políticas clássicas contiverem várias condições. Uma nova política que tem **o Exchange Ative Sync** como condição de aplicações de clientes configurada não suporta outras condições:   

![O Exchange ActiveSync não suporta as condições selecionadas](./media/policy-migration/08.png)

Se tiver uma nova política que tenha **o Exchange Ative Sync** como condição de aplicações de clientes configurada, tem de se certificar de que todas as outras condições não estão configuradas. 

![Condições de acesso condicional](./media/policy-migration/16.png)
 
As políticas clássicas baseadas em aplicativos para o Exchange Online que incluem **o Exchange Ative Sync** como condição de aplicações de clientes permitem plataformas de dispositivos **suportadas** e **não apoiadas.** Embora não seja possível configurar plataformas individuais de dispositivos numa nova política relacionada, pode limitar o suporte apenas a [plataformas de dispositivos suportados.](concept-conditional-access-conditions.md#device-platforms) 

![Acesso Condicional selecione Exchange ActiveSync](./media/policy-migration/65.png)

Pode consolidar várias políticas clássicas que incluem **o Exchange Ative Sync** como condição de aplicações de clientes se tiverem:

- Apenas **Exchange Ative Sync** como condição 
- Vários requisitos para a concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivos do portal clássico Azure 
- Uma política clássica baseada em aplicativos no portal de proteção de aplicações Intune 
 
Neste caso, pode consolidar as suas políticas clássicas numa nova política que tenha ambos os requisitos selecionados.

![Controlos de concessão de acesso condicional](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivos

As políticas clássicas com controlos baseados em aplicações são pré-configuradas com iOS e Android como condição da plataforma do dispositivo. 

Numa nova política, é necessário selecionar as plataformas do [dispositivo](concept-conditional-access-conditions.md#device-platforms) que pretende suportar individualmente.

![Seleção de plataformas de dispositivos de acesso condicional](./media/policy-migration/41.png)

## <a name="next-steps"></a>Passos seguintes

- [Utilize o modo apenas de relatório para acesso condicional para determinar o impacto de novas decisões políticas.](concept-conditional-access-report-only.md)
- Se quiser saber como configurar uma política de acesso condicional, consulte políticas [comuns de acesso condicional.](concept-conditional-access-policy-common.md)
- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o artigo [Como: Planeie a sua implementação de Acesso Condicional no Diretório Ativo Azure](plan-conditional-access.md). 
