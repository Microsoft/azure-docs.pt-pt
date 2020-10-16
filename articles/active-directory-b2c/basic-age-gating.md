---
title: Habilitar a idade em Azure Ative Directory B2C / Microsoft Docs
description: Saiba como identificar menores usando a sua aplicação.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bd93f9062f8446ce20436a7a04e2054aaf5be71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386136"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Ativar a idade gating em Azure Ative Directory B2C

>[!IMPORTANT]
>Esta funcionalidade está em pré-visualização pública. Não utilize recurso para aplicações de produção.
>

A gating de idade em Azure Ative Directory B2C (Azure AD B2C) permite identificar menores que queiram usar a sua aplicação. Pode optar por impedir que o menor assine a inscrição. Os utilizadores também podem voltar à aplicação e identificar a sua faixa etária e o seu estado de consentimento parental. Azure AD B2C pode bloquear menores sem o consentimento dos pais. O Azure AD B2C também pode ser criado para permitir que o pedido decida o que fazer com menores.

Depois de ativar a idade no fluxo do seu [utilizador,](user-flow-overview.md)os utilizadores são questionados sobre quando nasceram e em que país/região vivem. Se um utilizador assinar que não tenha introduzido previamente a informação, terá de a introduzir na próxima vez que iniciar a sua inscrição. As regras são aplicadas sempre que um utilizador assina.

Azure AD B2C utiliza a informação que o utilizador introduz para identificar se é menor. O campo **AgeGroup** é então atualizado na sua conta. O valor pode `null` ser, `Undefined` , , e `Minor` `Adult` `NotAdult` .  Os **campos ageGroup** e **consentProvidedForMinor** são então utilizados para calcular o valor da **legalAgeGroupClassification**.

A idade envolve dois valores de idade: a idade em que alguém já não é considerado menor, e a idade em que um menor deve ter o consentimento dos pais. A tabela a seguir enumera as regras de idade utilizadas para a definição de um menor e um menor que exigem consentimento.

| País/Região | Nome país/região | Idade de consentimento menor | Idade menor |
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
| DT | Chade | Nenhum | 21 |
| TH | Tailândia | Nenhum | 20 |
| TW | Taiwan | Nenhum | 20 |
| EUA | Estados Unidos da América | 13 | 18 |

## <a name="age-gating-options"></a>Opções de gating idade

### <a name="allowing-minors-without-parental-consent"></a>Permitir menores sem consentimento dos pais

Para fluxos de utilizador que permitem a inscrição, o sismo ou ambos, pode optar por permitir menores sem consentimento na sua aplicação. Os menores sem consentimento parental podem inscrever-se ou inscrever-se normalmente e o Azure AD B2C emite um sinal de identificação com a reclamação **legalAgeGroupClassification.** Esta alegação define a experiência que os utilizadores têm, como a recolha do consentimento dos pais e a atualização do **consentimentoProvidedForMinor.**

### <a name="blocking-minors-without-parental-consent"></a>Bloquear menores sem consentimento dos pais

Para fluxos de utilizador que permitem a inscrição, o sismo ou ambos, pode optar por bloquear menores sem o consentimento da aplicação. As seguintes opções estão disponíveis para o manuseamento de utilizadores bloqueados no Azure AD B2C:

- Envie um JSON de volta ao pedido - esta opção envia uma resposta de volta à aplicação de que um menor foi bloqueado.
- Mostrar uma página de erro - é mostrado ao utilizador uma página informando-os de que não podem aceder à aplicação.

## <a name="set-up-your-tenant-for-age-gating"></a>Instale o seu inquilino para a idade de gating

Para utilizar a idade num fluxo de utilizador, você precisa configurar o seu inquilino para ter propriedades adicionais.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior. Selecione o diretório que contém o seu inquilino.
2. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure, procure e selecione **Azure AD B2C**.
3. Selecione **Propriedades** para o seu inquilino no menu à esquerda.
2. Na secção **de gating da Idade,** clique em **Configurar**.
3. Aguarde que a operação esteja concluída e o seu inquilino será preparado para a idade.

## <a name="enable-age-gating-in-your-user-flow"></a>Ativar a gating de idade no fluxo do seu utilizador

Depois de o seu inquilino ser configurado para usar a idade, pode então utilizar esta funcionalidade nos [fluxos](user-flow-versions.md) de utilizador onde está ativada. Permite a formação de idades com os seguintes passos:

1. Crie um fluxo de utilizador que tenha a idade ativada.
2. Depois de criar o fluxo do utilizador, selecione **Propriedades** no menu.
3. Na secção **de gating idade,** selecione **Ativado**.
4. Em seguida, decide como pretende gerir utilizadores que se identificam como menores. Para **iniciar s sesuda ou iniciar s inscrição,** selecione `Allow minors to access your application` ou `Block minors from accessing your application` . Se forem selecionados menores de bloqueio, selecione `Send a JSON back to the application` ou `Show an error message` .




