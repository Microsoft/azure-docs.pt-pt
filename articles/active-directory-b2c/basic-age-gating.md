---
title: Ativar o controlo de idade no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como identificar os menores que utilizam a sua aplicação.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f8309424b8e1eed97d66fbd168444418cc1f98fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383937"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Ativar o controlo de idade no Azure Active Directory B2C

>[!IMPORTANT]
>Esta funcionalidade está em pré-visualização pública. Não utilize a funcionalidade para aplicações de produção. 
>

Controlo de idade no Azure Active Directory (Azure AD) B2C permite-lhe identificar os menores que pretende utilizar a sua aplicação. Pode optar por bloquear o menor de iniciar sessão na aplicação. Os utilizadores também podem voltar para a aplicação e identificar o respetivo grupo etário e o respetivo estado de consentimento dos pais. O Azure AD B2C, pode bloquear os menores sem o consentimento dos pais. O Azure AD B2C também pode ser configurado para permitir que o aplicativo decidir o que fazer com os menores.

Depois de ativar o controlo de idade no seu [fluxo de utilizador](active-directory-b2c-reference-policies.md), é pedido aos utilizadores quando eles nasceram e quais país residem em. Se um utilizador inicia sessão que ainda não foram inseridas anteriormente as informações, precisam de introduzi-lo da próxima vez que iniciarem sessão. As regras são aplicadas sempre que um utilizador inicia sessão.

O Azure AD B2C utiliza as informações que o usuário insere para identificar se eles são menor. O **ageGroup** campo, em seguida, é atualizado na respetiva conta. O valor pode ser `null`, `Undefined`, `Minor`, `Adult`, e `NotAdult`.  O **ageGroup** e **consentProvidedForMinor** campos, em seguida, são utilizados para calcular o valor de **legalAgeGroupClassification**.

Controlo de idade envolve dois valores de idade: a idade que alguém já não é considerado menor e a idade em que menor tem de ter o consentimento dos pais. A tabela seguinte lista as regras de idade que são utilizadas para a definição de pequenas e uma janela de consentimento que exigem que pequenas.

| País | Nome do país | Consentimento do menor de idade | Idade secundária |
| ------- | ------------ | ----------------- | --------- |
| Predefinição | Nenhuma | Nenhuma | 18 |
| AE | Emirados Árabes Unidos | Nenhuma | 21 |
| AT | Áustria | 14 | 18 |
| BE | Bélgica | 14 | 18 |
| BG | Bulgária | 16 | 18 |
| BH | Barém | Nenhuma | 21 |
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
| HR | Croácia | 16 | 18 |
| HU | Hungria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| TI | Itália | 16 | 18 |
| KR | Coreia, República da | 14 | 18 |
| LT | Lituânia | 16 | 18 |
| LU | Luxemburgo | 16 | 18 |
| LV | Letónia | 16 | 18 |
| MT | Malta | 16 | 18 |
| ND | Namíbia | Nenhuma | 21 |
| NL | Países Baixos | 16 | 18 |
| PL | Polónia | 13 | 18 |
| PT | Portugal | 16 | 18 |
| RO | Roménia | 16 | 18 |
| SE | Suécia | 13 | 18 |
| SG | Singapura | Nenhuma | 21 |
| SI | Eslovénia | 16 | 18 |
| SK | Eslováquia | 16 | 18 |
| TD | Chade | Nenhuma | 21 |
| TH | Tailândia | Nenhuma | 20 |
| TW | Taiwan | Nenhuma | 20 | 
| EUA | Estados Unidos | 13 | 18 |

## <a name="age-gating-options"></a>Opções de controlo de idade
 
### <a name="allowing-minors-without-parental-consent"></a>Permitir que os menores sem o consentimento dos pais

Para os fluxos de utilizador que permitem a inscrição, início de sessão, ou ambas, pode optar por permitir que menores acedam sem o consentimento na sua aplicação. Os menores sem o consentimento dos pais têm permissão para iniciar sessão ou Inscreva-se como normal e o Azure AD B2C emite um token de ID com o **legalAgeGroupClassification** de afirmação. Esta afirmação define a experiência que os utilizadores têm, como autorização parental a recolher e a atualizar o **consentProvidedForMinor** campo.

### <a name="blocking-minors-without-parental-consent"></a>Bloquear os menores sem o consentimento dos pais

Para fluxos de utilizador que permitem a inscrição, início de sessão ou ambos, pode optar por bloquear os menores sem o consentimento da aplicação. As seguintes opções estão disponíveis para manipular a utilizadores bloqueados no Azure AD B2C:

- Enviar um JSON de volta para a aplicação - esta opção envia uma resposta de volta para a aplicação que o menor foi bloqueado.
- Mostre uma página de erro - o utilizador é apresentado uma página informar que eles não é possível aceder à aplicação.

## <a name="set-up-your-tenant-for-age-gating"></a>Configurar o seu inquilino para o controlo de idade

Para utilizar o controlo de idade num fluxo de utilizador, terá de configurar o seu inquilino ter propriedades adicionais.

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior. Selecione o diretório que contém o seu inquilino. 
2. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **do Azure AD B2C**.
3. Selecione **propriedades** para o seu inquilino no menu à esquerda.
2. Sob o **controlo de idade** secção, clique em **configurar**.
3. Aguarde pela conclusão da operação e o seu inquilino será configurado para controlo de idade.

## <a name="enable-age-gating-in-your-user-flow"></a>Ativar o controlo de idade no seu fluxo de utilizador

Depois do seu inquilino estiver configurado para controlo de idade de utilização, em seguida, pode utilizar esta funcionalidade no [fluxos de utilizador](user-flow-versions.md) onde ele está habilitado. Ativar o controlo de idade com os seguintes passos:

1. Crie um fluxo de utilizador que tenha o controlo de idade ativada.
2. Depois de criar o fluxo de utilizador, selecione **propriedades** no menu.
3. Na **controlo de idade** secção, selecione **ativado**.
4. Em seguida, decidir como pretende gerir utilizadores identificar como os menores. Para **inscrição ou início de sessão**, selecione `Allow minors to access your application` ou `Block minors from accessing your application`. Se bloquear os menores é selecionado, selecione `Send a JSON back to the application` ou `Show an error message`. 




