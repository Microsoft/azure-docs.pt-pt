---
title: Unidade de teste hospedada | Azure Marketplace
description: Como configurar uma unidade de teste de manutenção hospedada do Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827523"
---
# <a name="hosted-test-drive"></a>Versão de Teste Alojada

Uma unidade de teste hospedada remove a complexidade da instalação pela Microsoft que hospeda e mantém o serviço que executa o teste de provisionamento e desprovisionamento do usuário. Este artigo destina-se a editores que têm sua oferta em AppSource ou que estão criando um novo e desejam oferecer um test drive hospedado, que se conecta com um cliente Dynamics 365 for Customer Engagement, Dynamics 365 for Finance and Operations ou Dynamics 365 Business central cópia.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um test drive

Navegue até a oferta existente ou crie uma nova oferta.

Selecione a opção Test Drive no menu lateral.

Selecione \'Sim\' para \'habilitar uma opção\' do Test Drive.

Forneça os campos a seguir na seção \'detalhes\'.

- **Descrição**: forneça uma visão geral do seu Test Drive. Esse texto será mostrado ao usuário enquanto o Test Drive estiver sendo provisionado. Esse campo oferece suporte a HTML se você quiser fornecer conteúdo formatado.
- **Manual do usuário**: carregue um manual de usuário Detalhado (arquivo do tipo. pdf) que ajuda a testar os usuários da unidade a entender como usar seu aplicativo.
- **Vídeo de demonstração do Test Drive**: opcionalmente, carregue um vídeo que apresente seu aplicativo.

Conceda permissão AppSource para provisionar e desprovisionar os usuários do Test Drive no seu locatário usando as instruções localizadas [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Nesta etapa, você irá gerar o \'Aplicativo Azure AD ID\' e \'valores aplicativo Azure ad chave\' mencionados abaixo.

Forneça os seguintes campos na seção \'\' de configuração técnica:

- **Tipo de unidade de teste**: escolha \'opção Microsoft Hosted (exemplo, Dynamics 365 for Customer Engagement) '. Isso indica que a Microsoft hospedará e manterá o serviço que executa o teste de provisionamento e desprovisionamento do usuário do Test Drive.
- **Máximo de unidades de teste simultâneas**: defina esse campo como o número de usuários simultâneos que podem ter uma unidade de teste ativa em um determinado momento. Cada usuário consumirá uma licença do Dynamics enquanto o Test Drive estiver ativo, portanto, você precisará garantir que tenha pelo menos essas muitas licenças do Dynamics disponíveis para os usuários do Test Drive. Valor recomendado de 3-5.
- **Duração da unidade de teste (horas)** : defina esse campo como o número de horas em que os usuários Test Drive estarão ativos. Após esse número de horas, o usuário será desprovisionado do seu locatário. Valor recomendado de 2-24 horas, dependendo da complexidade do seu aplicativo. O usuário sempre poderá solicitar outra unidade de teste se ficar sem tempo e quiser acessar a unidade de teste novamente.
- **URL da instância**: forneça uma URL para a qual o usuário da unidade de teste será navegado inicialmente quando iniciar a unidade de teste. Normalmente, essa é a URL da instância do Dynamics 365 que tem o aplicativo e os dados de exemplo instalados no. Valor de exemplo: https: \//testdrive. CRM. Dynamics. com
- **ID de locatário do Azure ad**: forneça a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, faça logon no portal do Azure e navegue até \'Azure Active Directory\' -\> selecione Propriedades no menu folha-\> copiar a ID do diretório. Valor de exemplo: 72f988bf-86f1-41AF-91ab-2d7cd0111234
- **ID de aplicativo Azure ad**: id da aplicativo Azure AD que você criou na etapa 7. \ exemplo valor: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave de aplicativo Azure ad**: segredo para o aplicativo Azure ad criado na etapa 7. \ valor de exemplo: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Nome do locatário do Azure ad**: forneça o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato de \<tenantname. \>onmicrosoft.com. Valor de exemplo: testdrive.onmicrosoft.com
- **URL da API Web da instância**: forneça a URL da API da Web para sua instância do Dynamics 365. Você pode recuperar esse valor fazendo logon em sua instância do Microsoft Dynamics 365 e navegando até configuração-\> personalização-\> recursos do desenvolvedor-\> API Web de instância (copiar esta URL). Valor de exemplo: https: \//testdrive. CRM. Dynamics. com/API/data/v 9.0 
- **Nome da função**: forneça o nome da função de segurança personalizada do Dynamics 365 que você criou para o Test Drive. Essa é a função que será atribuída aos usuários durante o teste de unidade. Valor de exemplo: testdriverole

## <a name="next-steps"></a>Passos seguintes

Quando estiver pronto, **publique** sua oferta, depois que seu aplicativo tiver passado para a certificação, você terá uma **Visualização** da sua oferta. Inicie um test drive na interface do usuário e verifique se as unidades de teste estão sendo executadas corretamente. Depois de se sentir confortável com sua oferta de visualização, agora é hora de **entrar em funcionamento!**
