---
title: Hospedado Test-Drive | Documentos da Microsoft
description: Como configurar uma mantenha uma versão de teste de hospedado do Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5057c0c781cb9ec60ecde7dd3f4bf96089b902df
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312687"
---
# <a name="hosted-test-drive"></a>Hospedado de teste de unidade

Uma versão de teste alojado retira a complexidade da configuração pelo alojamento da Microsoft e manter o serviço que executa o utilizador de teste de unidade de aprovisionamento e desaprovisionamento. Este artigo destina-se os publicadores com a oferta no AppSource ou estiver a criar um novo e que pretendem oferecer uma versão de teste de alojado, que liga a um do Dynamics 365 for Customer Engagement, do Dynamics 365 para finanças e operações ou o Dynamics 365 Business Central instância.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Navegue para a oferta existente ou crie uma nova oferta.

Selecione a opção de Test-Drive do menu do lado.

Selecione \'Sim\' para \'ativar um Test Drive\' opção.

Fornecer os campos seguintes no \'detalhes\' secção.

- **Descrição**: Forneça uma descrição geral da sua versão de teste. Esse texto será apresentado ao utilizador enquanto o Test-Drive está a ser aprovisionado. Este campo suporta HTML, se quiser fornecer conteúdo formatado.
- **Manual do usuário**: Carregar um manual de utilizador detalhadas (ficheiro de. pdf do tipo) que ajuda os utilizadores de Test-Drive compreender como utilizar a sua aplicação.
- **Testar o vídeo de demonstração de unidade**: Opcionalmente, carregar um vídeo que demonstra a sua aplicação.

Permissão de concessão AppSource para aprovisionar e desaprovisionar utilizadores de Test-Drive do seu inquilino ao seguir as instruções localizadas [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Neste passo, irá gerar o \'Id de aplicação do Azure AD\' e \'chave de aplicação do Azure AD\' valores mencionado abaixo.

Fornecer os campos seguintes no \'técnico configuração\' secção:

- **Tipo de teste de unidade de**: Escolha \'Microsoft Hosted (exemplo do Dynamics 365 Customer Engagement)' opção. Isto indica que a Microsoft irá alojar e manter o serviço que executa o utilizador de teste de unidade de aprovisionamento e desaprovisionamento.
- **Versões de teste de simultâneas máx.**: Defina este campo para o número de utilizadores em simultâneo que podem ter um Test Drive do Active Directory num determinado período de tempo. Cada utilizador irá consumir uma licença do Dynamics, enquanto a unidade de teste está ativa, por isso terá de garantir que tenha, pelo menos, tantos Dynamics licenças disponíveis para os utilizadores da versão de teste. Valor recomendado de 3 a 5.
- **Teste de unidade de duração (horas)**: Defina este campo para o número de horas dos utilizadores a que versão de teste ficará ativa para. Depois de tantas horas, o utilizador irá ser desaprovisionado do seu inquilino. Valor de 2 a 24 horas, consoante a complexidade da sua aplicação de recomendado. O usuário sempre pode pedir outro teste se ficar sem tempo e deseja acessar a unidade de teste novamente.
- **URL de instância**: Forneça um URL que o utilizador de teste de unidade será inicialmente direcionado durante o arranque a versão de teste. Isso normalmente é o URL da sua instância do Dynamics 365 que tenha a sua aplicação e dados de exemplo instalados no. Valor de exemplo: https:\//testdrive.crm.dynamics.com
- **ID de inquilino do Azure AD**: Forneça o ID do inquilino do Azure para a sua instância do Dynamics 365. Para obter este valor, inicie sessão no portal do Azure e navegue para \'do Azure Active Directory\'  - \> selecione propriedades no painel de menu -\> copiar o ID de diretório. Valor de exemplo: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID de aplicação do Azure AD**: ID da aplicação do AD Azure que criou no passo 7. \ valor de exemplo: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave da aplicação do Azure AD**: Segredo da aplicação do AD Azure criados no passo 7. \ valor de exemplo: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nome de inquilino do Azure AD**: Forneça o nome do inquilino do Azure para a sua instância do Dynamics 365. Utilize o formato \<tenantname.\> onmicrosoft.com. Valor de exemplo: testdrive.onmicrosoft.com
- **URL da Web API de instâncias**: Forneça o URL da API Web para a sua instância do Dynamics 365. Pode obter este valor ao iniciar sessão na sua instância do Microsoft Dynamics 365 e navegar para a definição -\> personalização -\> recursos para desenvolvedores -\> instância Web API (copiar esta URL). Valor de exemplo: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Nome da função**: Forneça o nome da função de segurança do Dynamics 365 personalizada que criou para a versão de teste. Esta é a função que será atribuída aos utilizadores durante a sua versão de teste. Valor de exemplo: testdriverole

## <a name="next-steps"></a>Passos Seguintes

Quando pronto **publicar** sua oferta, depois da aplicação foi aprovada a certificação, terá uma **pré-visualização** da sua oferta. Iniciar uma versão de teste na interface de Usuário e certifique-se de que as versões de teste estão sendo executadas corretamente. Assim que se sentir à vontade com a sua oferta de pré-visualização, agora é hora de **ir ao vivo!**
