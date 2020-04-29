---
title: Unidade de Teste Hospedada [ Unidade de Teste Anfitrião] Mercado Azure
description: Como configurar um Marketplace Hosted Test Drive
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278268"
---
# <a name="hosted-test-drive"></a>Versão de Teste Alojada

Um Test Drive hospedado remove a complexidade da configuração por hospedagem da Microsoft e mantém o serviço que executa o fornecimento e desprovisionamento do utilizador Test Drive. Este artigo é para editores que têm a sua oferta no AppSource ou estão a construir um novo e querem oferecer um Test Drive Hospedado, que se conecta com uma Dinâmica 365 para envolvimento com o cliente, Dinâmica 365 para Finanças e Operações, ou a instância Da Dinâmica 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Navegue para a oferta existente ou crie uma nova oferta.

Selecione a opção Test Drive no menu lateral.

Selecione \'Sim\' para \'ativar uma opção Test Drive.\'

Forneça os seguintes \'\' campos na secção Detalhes.

- **Descrição**: Forneça uma visão geral da sua unidade de teste. Este texto será mostrado ao utilizador enquanto o Test Drive está a ser aprovisionado. Este campo suporta HTML se quiser fornecer conteúdo formatado.
- **Manual do utilizador**: Faça upload de um manual detalhado do utilizador (ficheiro do tipo .pdf) que ajuda os utilizadores do Test Drive a entender como utilizar a sua App.
- **Test Drive Demo Video**: Opcionalmente faça upload de um vídeo que mostre a sua App.

Conceda a permissão appSource para fornecer e desprovisionar utilizadores de Test Drive no seu inquilino usando as instruções [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)localizadas .

Neste passo, você gerará \'os valores\' \'chave\' da app Azure AD Id e Azure AD App abaixo mencionados.

Fornecer os seguintes \'campos\' na secção de Configuração Técnica:

- **Tipo de Test** \'Drive : Escolha o Microsoft Hosted (exemplo Dynamics 365 para a opção de envolvimento com o cliente)». Isto indica que a Microsoft irá hospedar e manter o serviço que executa o fornecimento e desprovisionamento do utilizador Test Drive.
- **Max Simultâneo Test Drives**: Desloque este campo ao número de utilizadores simultâneos que podem ter uma Unidade de Teste ativa em qualquer ponto de tempo. Cada utilizador irá consumir uma licença Dynamics enquanto o Test Drive estiver ativo, pelo que terá de garantir que tem pelo menos estas muitas licenças De Dinâmica disponíveis para utilizadores de Test Drive. Valor recomendado de 3-5.
- **Duração do test drive (horas)**: Desloque este campo para o número de horas para as horas em que os utilizadores estarão ativos. Após tantas horas, o utilizador será desprovisionado do seu inquilino. Valor recomendado de 2 a 24 horas dependendo da complexidade da sua App. O utilizador pode sempre solicitar outro Test Drive se ficar sem tempo e pretender aceder novamente ao Test Drive.
- **URL de instância**: Forneça um URL para o que o utilizador test drive será inicialmente navegado quando iniciar a Unidade de Teste. Este é tipicamente o URL da sua instância Dynamics 365 que tem a sua App e dados de amostra instalados. Exemplo Valor:\/https: /testdrive.crm.dynamics.com
- **ID Azure AD Tenant**: Forneça o ID do Inquilino Azure para a sua Instância Dinâmica 365. Para recuperar este valor, inicie sessão \'no portal Azure e navegue para o Azure Ative\'  - \> Directory Select Properties a partir da lâmina do menu -\> Copie o ID do Diretório. Valor do exemplo: 72f988bf-86f1-41af-91ab-2d7cd0111234
- Id da **aplicação Azure AD**: ID da App Azure AD que criou no passo 7.\ Exemplo Valor: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave da aplicação Azure AD**: Segredo para a app Azure AD criada no passo 7.\ Exemplo valor: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- Nome do **Inquilino Azure AD**: Forneça o nome do Inquilino Azure para a sua Instância Dinâmica 365. Use o \<formato do nome do inquilino. \>onmicrosoft.com. Valor do exemplo: testdrive.onmicrosoft.com
- **Por exemplo Web API URL**: Forneça o URL Web API para a sua Instância Dinâmica 365. Pode recuperar este valor iniciando sessão na sua instância Microsoft\> Dynamics\> 365\> e navegando para Definição - Personalização - Recursos de Desenvolvimento - API Web de instância (Copiar este URL). Valor do exemplo: https:\//testdrive.crm.dynamics.com/api/data/v9.0
- **Nome**do papel : Forneça o nome da função de segurança personalizada da Dynamics 365 que criou para test drive. Esta é a função que será atribuída aos utilizadores durante o test drive. Valor exemplo: testdriverole

## <a name="next-steps"></a>Passos seguintes

Quando estiver pronto **para publicar** a sua oferta, depois de a sua aplicação ter aprovado a certificação, terá uma **pré-visualização** da sua oferta. Inicie uma Unidade de Teste na UI e verifique se as suas Unidades de Teste estão a funcionar corretamente. Uma vez que se sinta confortável com a sua oferta de pré-visualização, agora é hora de **ir ao vivo!**
