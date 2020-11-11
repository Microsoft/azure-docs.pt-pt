---
title: Configuração técnica de test drive, mercado comercial da Microsoft
description: Saiba mais sobre test drives. Os test drives permitem que novos clientes testem a sua oferta antes de se comprometerem com a compra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 6faecdd561d7c434d2948e70886d6f1b19115d9f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504837"
---
# <a name="test-drive-technical-configuration"></a>Configuração técnica da versão de teste

A opção de test drive no mercado comercial da Microsoft permite-lhe configurar uma visita prática e auto-guiada às principais funcionalidades do seu produto. Com um test drive, novos clientes podem experimentar a sua oferta antes de se comprometerem a comprá-la. Para mais informações, veja [o que é Test Drive?](what-is-test-drive.md)

Se já não pretender fornecer um test drive para a sua oferta, volte à página **de configuração da Oferta** e limpe a caixa de verificação de unidade de teste **Enable.** Nem todos os tipos de oferta têm um test drive disponível.

## <a name="azure-resource-manager-test-drive"></a>Unidade de teste do Gestor de Recursos Azure

Esta é a única opção de test drive para máquina virtual ou a aplicação Azure oferece e requer uma configuração bastante detalhada. Leia as secções abaixo para [obter detalhes de subscrição de implementação](#deployment-subscription-details) e [listas de unidade de teste,](#test-drive-listings)em seguida, continue com o tópico separado para [a configuração do test drive do Azure Resource Manager](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Unidade de teste hospedada

A Microsoft pode remover a complexidade da configuração de um test drive hospedando e mantendo o fornecimento e desprovisionamento de serviços. A configuração para este tipo de test drive é a mesma, independentemente de o test drive estar direcionado para um público dinâmico 365 customer engagement ou Dynamics 365 Operations.

- **Unidades de teste simultâneas máximas** (necessárias) – Desaprova o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Cada utilizador simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, por isso certifique-se de que tem licenças suficientes disponíveis para suportar o limite máximo definido. O valor recomendado é 3-5.

- **Duração da unidade de teste** (necessária) – Introduza o número de horas em que o test drive permanecerá ativo para cada cliente. Após este período, a sessão terminará e deixará de consumir uma das suas licenças. Recomendamos um valor de 2-24 horas dependendo da complexidade da sua oferta. Esta duração só pode ser definida em horas inteiras (por exemplo, "2 horas" é válida; "1,5 horas" não é). O utilizador pode solicitar uma nova sessão se ficar sem tempo e quiser aceder novamente ao test drive.

- **URL de exemplo** (obrigatório) – O URL onde o cliente iniciará o seu test drive. Tipicamente, o URL da sua instância Dynamics 365 executa a sua aplicação com dados de amostra instalados (por exemplo, `https://testdrive.crm.dynamics.com` ).

- **Exemplo Web API URL** (obrigatório) – Recupere o URL da Web API para a sua instância Dynamics 365, iniciando sessão na sua conta Microsoft 365 e navegando para **Configurações**  >  **Desativação**  >  **Desenvolvimento Recursos Desinsusitados Desemintado Recursos** Web  >  **API (SERVICE Root URL)** ), copiar o URL encontrado aqui (por exemplo, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nome de função** (obrigatório) – Forneça o nome de função de segurança que definiu no seu test drive Dynamics 365 personalizado, que será atribuído ao utilizador durante a sua unidade de teste (por exemplo, função de test-drive).

Para obter ajuda sobre como configurar o seu ambiente Dynamics 365 para test drive e conceder permissão appSource para provisão e deprovisionar utilizadores de test drive no seu inquilino, siga [estas instruções](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

## <a name="logic-app-test-drive"></a>Unidade de teste de aplicativo lógico

Este tipo de test drive não é hospedado pela Microsoft. Use-o para se conectar com uma oferta Dynamics 365 ou outro recurso personalizado, que engloba uma variedade de arquiteturas complexas de soluções. Para mais informações sobre a configuração de unidades de teste da Aplicação Lógica, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Envolvimento com o Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista de dropdown necessária e de seleção única) – Atualmente existem 26 regiões apoiadas pelo Azure onde o seu test drive pode ser disponibilizado. Os recursos para a sua aplicação Logic serão implementados na região que selecionar. Se a sua Aplicação Lógica tiver quaisquer recursos personalizados armazenados numa região específica, certifique-se de que a região está selecionada aqui. A melhor forma é implementar totalmente a sua App Lógica localmente na sua subscrição Azure no portal e verificar se funciona corretamente antes de escamar esta seleção.

- **Unidades de teste simultâneas máximas** (necessárias) – Desaprova o número máximo de clientes que podem utilizar o seu test drive de uma só vez. Estes test drives já estão implantados, permitindo que os clientes acedam instantaneamente aos mesmos sem esperar por uma implantação.

- **Duração do test drive** (obrigatório) – Introduza o tempo de duração em que a Unidade de Teste se mantenha ativa, em 4 de horas. A unidade de teste termina automaticamente após o fim deste período de tempo.

- **Nome do grupo de recursos Azure** (obrigatório) – Introduza o [grupo de recursos Azure](../azure-resource-manager/management/overview.md#resource-groups)) nome onde o seu test drive da Logic App é guardado.

- **Nome da aplicação lógica Azure** (obrigatório) – Introduza o nome da aplicação Logic que atribui a unidade de teste ao utilizador. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

- **Deprovision logic app name** (required) – Introduza o nome da aplicação Logic que desesprovisiona a unidade de teste uma vez que o cliente esteja terminado. Esta aplicação Logic deve ser guardada no grupo de recursos Azure acima.

## <a name="power-bi-test-drive"></a>Unidade de teste bi de potência

Os produtos que pretendam demonstrar um power bi visual interativo podem usar um link incorporado para partilhar um dashboard personalizado como o seu test drive, sem necessidade de mais configurações técnicas. Tudo o que precisa fazer aqui é carregar o seu URL de POWER BI incorporado.

Para obter mais informações sobre a criação de aplicações Power BI, veja [o que são aplicações Power BI?](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Detalhes da subscrição de implementação

Para permitir que a Microsoft implemente o test drive em seu nome, crie e forneça uma subscrição Azure separada e única (não necessária para as unidades de teste Power BI).

- **ID de subscrição Azure** (obrigatório para o Azure Resource Manager e aplicações Lógica) – Introduza o ID de subscrição para garantir o acesso aos serviços de conta Azure para reportar e faturar o uso de recursos. Recomendamos que considere [criar uma subscrição Azure separada](../cost-management-billing/manage/create-subscription.md) para usar para test drives se ainda não tiver uma. Pode encontrar o seu ID de subscrição Azure iniciando sessão no [portal Azure](https://portal.azure.com/) e navegando no separador **Subscrições** do menu do lado esquerdo. A seleção do separador apresentará o seu ID de subscrição (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do inquilino Azure AD** (obrigatório) – Insira o seu ID do inquilino Azure Ative [(AD).](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure](https://portal.azure.com/), selecione o separador Ative Directory no menu esquerdo, selecione **Propriedades** , em seguida, procure o número de ID do **diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID do inquilino da sua organização usando o seu endereço de nome de domínio em [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Nome do inquilino Azure AD** (obrigatório para Dynamic 365) – Insira o seu nome Azure Ative Directory (AD). Para encontrar este nome, inscreva-se no [portal Azure,](https://portal.azure.com/)no canto superior direito o nome do seu inquilino será listado no nome da sua conta.

- **ID da aplicação AD AZure** (obrigatório) – Introduza o ID da aplicação Azure Ative [(AD).](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) Para encontrar este ID, inscreva-se no [portal Azure,](https://portal.azure.com/)selecione o separador Ative Directory no menu esquerdo, selecione **as inscrições da App** , em seguida, procure o número de ID da **aplicação** listado (como). `50c464d3-4930-494c-963c-1e951d15360e`

- **Azure AD app cliente secreto** (obrigatório) – Insira o [segredo do](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)seu cliente de aplicação Azure AD). Para encontrar este valor, inscreva-se no [portal Azure](https://portal.azure.com/). Selecione o separador **Azure Ative Directory** no menu esquerdo, selecione **registos de Aplicações** e selecione a sua aplicação test drive. Em seguida, selecione **Certificados e segredos,** selecione **Novo segredo do cliente,** introduza uma descrição, selecione **Nunca** em **Expiração,** e escolha **Adicionar**. Certifique-se de copiar o valor. Não navegue para longe da página antes de copiar o valor.

## <a name="test-drive-listings"></a>Listas de test drive

A opção **de listagem test drive** encontrada no separador Test **drive** no Partner Center exibe os idiomas (e mercados) onde o seu test drive está disponível, atualmente em inglês (Estados Unidos) é a única localização disponível. Além disso, esta página apresenta o estado da listagem específica da linguagem e a data/hora que foi adicionada. Será necessário definir os detalhes do test drive (descrição, manual do utilizador, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (necessária): Descreva o seu test drive, o que será demonstrado, objetivos para o utilizador experimentar, funcionalidades a explorar e quaisquer informações relevantes para ajudar o utilizador a determinar se deve adquirir a sua oferta. Podem ser inseridos até 3.000 caracteres de texto neste campo.

- **Informações de acesso** (necessárias para o Azure Resource Manager e as unidades de teste lógicas): Explique o que um cliente precisa de saber para aceder e utilizar este test drive. Caminhe por um cenário para usar a sua oferta e exatamente o que o cliente deve saber para aceder a funcionalidades ao longo do test drive. Podem ser inseridos até 10.000 caracteres de texto neste campo.

- **Manual do Utilizador** (obrigatório): Uma passagem aprofundada da sua experiência de test drive. O Manual do Utilizador deve cobrir exatamente o que pretende que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer questões que possam ter. O ficheiro deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicionar vídeos** (opcional): Vídeos alojados noutros locais podem ser referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa ver um walk-through de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso as características da sua oferta e entender cenários que realçam os seus benefícios.
  - **Nome** (obrigatório)
  - **URL** (apenas YouTube ou Vimeo; necessário)
  - **Miniatura** (533 x 324 pixels) – A imagem deve estar em formato PNG.

Se estiver atualmente a criar o seu test drive no Partner Center, **selecione Guardar o rascunho** antes de continuar.

## <a name="additional-resources"></a>Recursos Adicionais

## <a name="next-steps"></a>Passos seguintes

- [Melhores práticas de test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF; certifique-se de que o seu bloqueador pop-up está desligado)
- [Atualizar uma oferta existente no mercado comercial](partner-center-portal/update-existing-offer.md)
