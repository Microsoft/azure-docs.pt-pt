---
title: Tutorial para configurar Azure Ative Directory B2C com LexisNexis
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com a LexisNexis, que é um serviço de validação de perfis e identidade e é utilizado para verificar a identificação do utilizador e fornecer avaliações abrangentes de risco com base no dispositivo do utilizador.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9bec7ffe28fbcdafd365f9867ebecaee5d2647e5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953695"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Tutorial para configurar LexisNexis com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure AD B2C com [a LexisNexis.](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) A LexisNexis fornece uma variedade de soluções, pode encontrá-las [aqui.](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) Neste tutorial de amostra, vamos cobrir a solução **ThreatMetrix** da LexisNexis. ThreatMetrix é um serviço de validação de perfis e identidade. É usado para verificar a identificação do utilizador e fornecer avaliações abrangentes de risco com base no dispositivo do utilizador.

Esta integração faz perfis com base em algumas informações do utilizador, que são fornecidas pelo utilizador durante o fluxo de inscrição. O ThreatMetrix determina se o utilizador deve continuar a fazer login ou não. Os seguintes atributos são considerados na análise de risco da ThreatMetrix:

- E-mail
- Número de Telefone
- Informações de perfis recolhidas na máquina do utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração ThreatMetrix inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador, também conhecido como fornecedor de identidade

- ThreatMetrix – O serviço ThreatMetrix recolhe as entradas fornecidas pelo utilizador e combina-as com informações de perfis recolhidas na máquina do utilizador para verificar a segurança da interação do utilizador.

- Custom Rest API – Esta API implementa a integração entre o Azure AD B2C e o serviço ThreatMetrix.

O seguinte diagrama de arquitetura mostra a implementação.

![screenshot para lexisnexis-arquitetura-diagrama](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Passo | Descrição |
|:--------------|:-------------|
|1. | O utilizador chega a uma página de login. O utilizador seleciona a inscrição para criar uma nova conta e introduzir informações na página. Azure AD B2C recolhe os atributos do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a LexisNexis API poderia consumir. Depois, envia-o para a LexisNexis.  
| 4. | A LexisNexis consome a informação e processa-a para validar a identificação do utilizador com base na análise de risco. Em seguida, devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia de volta informações relevantes para Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta de Falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de Sucesso, o utilizador é autenticado e tem acesso.

## <a name="onboard-with-lexisnexis"></a>A bordo com LexisNexis

1. Para criar uma conta LexisNexis, contacte [a LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Crie uma política LexisNexis que satisfaça os seus requisitos. Utilize a documentação disponível [aqui.](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

>[!NOTE]
> O nome da apólice será usado mais tarde.

Assim que uma conta for criada, receberá a informação de que necessita para a configuração da API. As seguintes secções descrevem o processo.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Configure Azure AD B2C com LexisNexis

### <a name="part-1---deploy-the-api"></a>Parte 1 - Implantar a API

Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) fornecido para um serviço Azure. O código pode ser publicado a partir do Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

### <a name="part-2---configure-the-api"></a>Parte 2 - Configurar a API

As definições de aplicação podem ser [configuradas no serviço app em Azure.](../app-service/configure-common.md#configure-app-settings)  Com este método, as definições podem ser configuradas de forma segura sem as verificar num repositório. Terá de fornecer as seguintes definições à API de Descanso:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|ThreatMetrix: Url | Configuração da conta ThreatMetrix |     |
|ThreatMetrix:OrgId | Configuração da conta ThreatMetrix |     |
|ThreatMetrix:ApiKey |Configuração da conta ThreatMetrix|  |
|ThreatMetrix: Política | Nome da política criada na ThreatMetrix | |
| BasicAuth:ApiUsername |Definir um nome de utilizador para a API| O nome de utilizador será utilizado na configuração Azure AD B2C
| BasicAuth:ApiPassword | Definir uma palavra-passe para a API | A palavra-passe será usada na configuração AZURE AD B2C

### <a name="part-3---deploy-the-ui"></a>Parte 3 - Implementar a UI

Esta solução utiliza modelos de UI personalizados que são carregados por Azure AD B2C. Estes modelos de UI fazem o perfil que é enviado diretamente para o serviço ThreatMetrix.

Consulte estas [instruções](./custom-policy-ui-customization.md#custom-page-content-walkthrough) para implantar os [ficheiros de UI incluídos](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) numa conta de armazenamento de bolhas. As instruções incluem a criação de uma conta de armazenamento de bolhas, a configuração do CORS e o acesso do público.

A UI é baseada no modelo azul do [oceano.](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue) Todas as ligações dentro da UI devem ser atualizadas para se referirem à localização implantada. Na pasta UI, encontre e https://yourblobstorage/blobcontainer substitua pelo local implantado.

### <a name="part-4---create-api-policy-keys"></a>Parte 4 - Criar chaves de política da API

Consulte este [documento](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) e crie duas teclas políticas – uma para o nome de utilizador da API e outra para a palavra-passe API que definiu acima.

A política da amostra utiliza estes nomes-chave:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Parte 5 - Atualizar o URL da API

Na política fornecida do [TrustFrameworkExtensions,](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)encontre o perfil técnico nomeado `Rest-LexisNexus-SessionQuery` e atualize o `ServiceUrl` item dos metadados com a localização da API implantada acima.

### <a name="part-6---update-ui-url"></a>Parte 6 - Atualizar URL UI

Na política fornecida do [TrustFrameworkExtensions,](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)faça uma pesquisa e substitua para procurar https://yourblobstorage/blobcontainer/ pela localização para a localização para onde os ficheiros UI são implantados.

>[!NOTE]
> Como uma boa prática, recomendamos que os clientes adicionem a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que as informações serão enviadas para serviços de terceiros para verificação de identidade.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Parte 7 - Configurar a política Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para descarregar o [pacote de iniciação de Contas Locais](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurar a [política](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) para o inquilino Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para se relacionar com o seu inquilino específico.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **fluxos de utilizador**.

2. Selecione o seu **Fluxo de Utilizador** previamente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. Log-out

6. Passar pelo fluxo de entrada  

7. O puzzle ThreatMetrix aparecerá depois de **entrares.**

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)