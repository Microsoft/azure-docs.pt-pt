---
title: Permitir a marcha da idade no Diretório Ativo Azure B2C  Microsoft Docs
description: Saiba como identificar menores usando a sua aplicação.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1fc63b222fd2f08bb4b5596d58f825c8f6b1910e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836241"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Ativar a gating idade no Diretório Ativo Azure B2C

>[!IMPORTANT]
>Esta funcionalidade está em pré-visualização pública. Não utilize recurso para aplicações de produção.
>

A marcha etária no Diretório Ativo Azure B2C (Azure AD B2C) permite identificar menores que pretendam utilizar a sua aplicação. Pode optar por impedir o menor de assinar a candidatura. Os utilizadores também podem voltar à aplicação e identificar a sua faixa etária e o seu estado de consentimento parental. Azure AD B2C pode bloquear menores sem o consentimento dos pais. O Azure AD B2C também pode ser criado para permitir que a aplicação decida o que fazer com menores.

Depois de permitir a marcha da idade no [fluxo](user-flow-overview.md)do utilizador, os utilizadores são questionados quando nasceram e em que país/região vivem. Se um utilizador assinar que não tenha introduzido previamente a informação, terá de entrar nela da próxima vez que iniciar a sua inscrição. As regras são aplicadas sempre que um utilizador entra.

O Azure AD B2C utiliza a informação que o utilizador introduz para identificar se é menor. O campo **ageGroup** é então atualizado na sua conta. O valor pode ser `null`, `Undefined`, `Minor`, `Adult`e `NotAdult`.  Os campos **ageGroup** e **consentProvidedForMinor** são então utilizados para calcular o valor da **classificação legalAgeGroup .**

A marcha etária envolve dois valores etários: a idade em que alguém já não é considerado menor, e a idade em que um menor deve ter o consentimento dos pais. A tabela que se segue enumera as regras de idade utilizadas para a definição de um menor e um menor que exigem o consentimento.

| País/Região | Nome país/região | Idade de consentimento menor | Idade menor |
| -------------- | ------------------- | ----------------- | --------- |
| Predefinição | Nenhuma | Nenhuma | 18 |
| AE | Emirados Árabes Unidos | Nenhuma | 21 |
| AT | Áustria | 14 | 18 |
| BE | Bélgica | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Bahrain | Nenhuma | 21 |
| CM | Camarões | Nenhuma | 21 |
| CY | Chipre | 16 | 18 |
| CZ | República Checa | 16 | 18 |
| DE | Alemanha | 16 | 18 |
| DK | Dinamarca | 16 | 18 |
| EE | Estónia | 16 | 18 |
| EG | Egito | Nenhuma | 21 |
| ES | Espanha | 13 | 18 |
| FR | França | 16 | 18 |
| GB | Reino Unido | 13 | 18 |
| GR | Grécia | 16 | 18 |
| RH | Croácia | 16 | 18 |
| HU | Hungria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| TI | Itália | 16 | 18 |
| KR | Coreia, República da | 14 | 18 |
| LT | Lituânia | 16 | 18 |
| LU | Luxemburgo | 16 | 18 |
| LV | Letónia | 16 | 18 |
| MT | Malta | 16 | 18 |
| N/D | Namíbia | Nenhuma | 21 |
| NL | Países Baixos | 16 | 18 |
| PL | Polónia | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Roménia | 16 | 18 |
| SE | Suécia | 13 | 18 |
| SG | Singapura | Nenhuma | 21 |
| SI | Eslovénia | 16 | 18 |
| SK | Eslováquia | 16 | 18 |
| TD | Rio Chade | Nenhuma | 21 |
| TH | Tailândia | Nenhuma | 20 |
| TW | Taiwan | Nenhuma | 20 |
| EUA | Estados Unidos | 13 | 18 |

## <a name="age-gating-options"></a>Opções de gating de idade

### <a name="allowing-minors-without-parental-consent"></a>Permitir menores sem consentimento parental

Para fluxos de utilizador que permitam o inscrição, o inserção ou ambos, pode optar por permitir menores sem consentimento na sua aplicação. Os menores sem consentimento dos pais podem inscrever-se ou inscrever-se normalmente e o Azure AD B2C emite um documento de identificação com a alegação **legalAgeGroupClassification.** Esta alegação define a experiência que os utilizadores têm, como recolher o consentimento dos pais e atualizar o campo **ConsentProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Bloquear menores sem consentimento dos pais

Para fluxos de utilizador que permitam o inscrição, o inserção ou ambos, pode optar por bloquear menores sem consentimento da aplicação. Estão disponíveis as seguintes opções para manusear utilizadores bloqueados no Azure AD B2C:

- Envie um JSON de volta para a aplicação - esta opção envia uma resposta de volta ao pedido que um menor foi bloqueado.
- Mostre uma página de erro - o utilizador é mostrado uma página informando-os de que não pode aceder à aplicação.

## <a name="set-up-your-tenant-for-age-gating"></a>Instale o seu inquilino para a gating idade

Para utilizar a idade em um fluxo de utilizador, você precisa configurar o seu inquilino para ter propriedades adicionais.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior. Selecione o diretório que contém o seu inquilino.
2. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure, procure e selecione **Azure AD B2C**.
3. Selecione **Propriedades** para o seu inquilino no menu à esquerda.
2. Sob a secção **de marcha da Idade,** clique em **Configurar**.
3. Aguarde a operação e o seu inquilino será preparado para a marcha da idade.

## <a name="enable-age-gating-in-your-user-flow"></a>Ativar a marcha da idade no fluxo do utilizador

Depois de o seu inquilino ser configurado para usar a marcha etária, pode então utilizar esta funcionalidade nos [fluxos](user-flow-versions.md) de utilizador onde está ativada. Permite a marcha da idade com os seguintes passos:

1. Crie um fluxo de utilizador que tenha a marcha da idade ativada.
2. Depois de criar o fluxo do utilizador, selecione **Propriedades** no menu.
3. Na secção Idade de **marcha,** selecione **Ativado**.
4. Em seguida, decide como pretende gerir utilizadores que se identifiquem como menores. Para **iniciar sessão ou iniciar sessão,** selecione `Allow minors to access your application` ou `Block minors from accessing your application`. Se estiver selecionado o bloqueio de menores, selecione `Send a JSON back to the application` ou `Show an error message`.




