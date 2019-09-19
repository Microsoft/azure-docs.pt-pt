---
title: Habilitar a retenção de idade no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como identificar menores usando seu aplicativo.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7eb4d8e784acc659f6661ef6efbdb06816b142c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064459"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Habilitar a retenção de idade no Azure Active Directory B2C

>[!IMPORTANT]
>Esta funcionalidade está em pré-visualização pública. Não use o recurso para aplicativos de produção.
>

A retenção de idade no Azure Active Directory B2C (Azure AD B2C) permite que você identifique os pequenos que desejam usar seu aplicativo. Você pode optar por bloquear a secundária de entrar no aplicativo. Os usuários também podem voltar para o aplicativo e identificar sua faixa etária e o status de consentimento dos pais. Azure AD B2C pode bloquear menores sem o consentimento dos pais. Azure AD B2C também pode ser configurado para permitir que o aplicativo decida o que fazer com os pequenos.

Depois de habilitar a retenção de idade em seu [fluxo de usuário](active-directory-b2c-reference-policies.md), os usuários são solicitados quando eles foram gerados e em qual país/região eles residem. Se um usuário entrar que não inseriu as informações anteriormente, ele precisará inseri-la na próxima vez que entrar. As regras são aplicadas toda vez que um usuário entra.

Azure AD B2C usa as informações que o usuário insere para identificar se elas são secundárias. O campo **ageGroup** é então atualizado em sua conta. O valor pode ser `null`, `Undefined`, `Minor`, `Adult`e. `NotAdult`  Os campos **ageGroup** e **consentProvidedForMinor** são então usados para calcular o valor de **legalAgeGroupClassification**.

A retenção age envolve dois valores de idade: a idade que alguém não é mais considerado um secundário e a idade em que um secundário deve ter um consentimento de pais. A tabela a seguir lista as regras de idade que são usadas para definir um consentimento secundário e um secundário que exigem autorização.

| País/Região | Nome do país/região | Idade de consentimento secundária | Idade secundária |
| -------------- | ------------------- | ----------------- | --------- |
| Predefinição | Nenhum | Nenhum | 18 |
| AE | Emirados Árabes Unidos | Nenhum | 21 |
| AT | Áustria | 14 | 18 |
| BE | Bélgica | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Barém | Nenhum | 21 |
| CM | Camarões | Nenhum | 21 |
| CY | Chipre | 16 | 18 |
| CZ | República Checa | 16 | 18 |
| DE | Alemanha | 16 | 18 |
| DK | Dinamarca | 16 | 18 |
| EE | Estónia | 16 | 18 |
| EG | Egito | Nenhum | 21 |
| ES | Espanha | 13 | 18 |
| FR | França | 16 | 18 |
| GB | Reino Unido | 13 | 18 |
| GR | Grécia | 16 | 18 |
| HR | Croácia | 16 | 18 |
| HU | Hungria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| TI | Itália | 16 | 18 |
| KR | Coreia, República da | 14 | 18 |
| LT | Lituânia | 16 | 18 |
| LU | Luxemburgo | 16 | 18 |
| LV | Letónia | 16 | 18 |
| MT | Malta | 16 | 18 |
| ND | Namíbia | Nenhum | 21 |
| NL | Países Baixos | 16 | 18 |
| PL | Polónia | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Roménia | 16 | 18 |
| SE | Suécia | 13 | 18 |
| SG | Singapura | Nenhum | 21 |
| SI | Eslovénia | 16 | 18 |
| SK | Eslováquia | 16 | 18 |
| TD | Chade | Nenhum | 21 |
| TH | Tailândia | Nenhum | 20 |
| TW | Taiwan | Nenhum | 20 |
| EUA | Estados Unidos | 13 | 18 |

## <a name="age-gating-options"></a>Opções de retenção etária

### <a name="allowing-minors-without-parental-consent"></a>Permitindo menores sem o consentimento dos pais

Para fluxos de usuário que permitem a inscrição, a entrada ou ambos, você pode optar por permitir menores sem consentir o seu aplicativo. Os pequenos sem o consentimento dos pais têm permissão para entrar ou se inscrever como normal e Azure AD B2C emite um token de ID com a Declaração **legalAgeGroupClassification** . Essa declaração define a experiência que os usuários têm, como coletar o consentimento dos pais e atualizar o campo **consentProvidedForMinor** .

### <a name="blocking-minors-without-parental-consent"></a>Bloqueios secundários sem consentimento dos pais

Para fluxos de usuário que permitem inscrição, entrada ou ambos, você pode optar por bloquear os pequenos sem autorização do aplicativo. As opções a seguir estão disponíveis para lidar com usuários bloqueados no Azure AD B2C:

- Enviar um JSON de volta para o aplicativo-essa opção envia uma resposta de volta ao aplicativo que um secundário foi bloqueado.
- Mostrar uma página de erro-o usuário mostra uma página informando que eles não podem acessar o aplicativo.

## <a name="set-up-your-tenant-for-age-gating"></a>Configurar seu locatário para a retenção de idade

Para usar a retenção de idade em um fluxo de usuário, você precisa configurar seu locatário para ter propriedades adicionais.

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior. Selecione o diretório que contém seu locatário.
2. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure ad B2C**.
3. Selecione **Propriedades** para seu locatário no menu à esquerda.
2. Na seção **retenção de idade** , clique em **Configurar**.
3. Aguarde a conclusão da operação e seu locatário será configurado para a retenção de idade.

## <a name="enable-age-gating-in-your-user-flow"></a>Habilitar a retenção de idade em seu fluxo de usuário

Depois que o locatário for configurado para usar a retenção de idade, você poderá usar esse recurso nos [fluxos de usuário](user-flow-versions.md) onde ele está habilitado. Você habilita a retenção de idade com as seguintes etapas:

1. Crie um fluxo de usuário que tenha a retenção de idade habilitada.
2. Depois de criar o fluxo de usuário, selecione **Propriedades** no menu.
3. Na seção **retenção etária** , selecione **habilitado**.
4. Em seguida, você decide como deseja gerenciar os usuários que identificam como menores. Para **inscrever-se ou entrar**, selecione `Allow minors to access your application` ou `Block minors from accessing your application`. Se bloqueios secundários estiver selecionado, selecione `Send a JSON back to the application` ou `Show an error message`.




