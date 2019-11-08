---
title: Unidade de teste do aplicativo lógico | Azure Marketplace
description: Explica como criar seu Test Drive que se conecta a uma instância do Dynamics AX/CRM ou a qualquer outro recurso além do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 87170344f7467770829cbd8ba7d8936a9e39c0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824589"
---
<a name="logic-app-test-drive"></a>Unidade de teste do aplicativo lógico
====================

Este artigo destina-se a editores que têm sua oferta no AppSource e desejam criar seu Test Drive que se conecta a uma instância do Dynamics AX/CRM ou a qualquer outro recurso além do Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Como criar uma unidade de teste de aplicativo lógico
-----------------------------------

A documentação do Test Drive para unidades de teste de aplicativo lógico atualmente ainda está no GitHub para [operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e envolvimento com o [cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), vá lá para ler mais.

<a name="how-to-publish-a-test-drive"></a>Como publicar um test drive
---------------------------

Agora que o seu test drive foi criado, esta seção percorre cada um dos campos necessários para que você publique com êxito a unidade de teste.

![Habilitar o recurso Test Drive](./media/azure-resource-manager-test-drive/howtopub1.png)

O primeiro campo e mais importante é alternar se você deseja que o teste do formulário com todos os campos obrigatórios seja apresentado para você preencher. Quando você seleciona **não,** o formulário fica desabilitado e, se você republicar com a unidade de teste desabilitada, sua unidade de teste será removida da produção.

*Observação*: se houver qualquer unidade de teste usada ativamente pelos usuários, essas unidades de teste continuarão a ser executadas até que sua sessão expire.

### <a name="details"></a>Detalhes

A próxima seção a ser preenchida é os detalhes sobre a oferta do Test Drive.

![Detalhes do driver de teste](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição-** *[campo obrigatório]* é aqui que você escreve a descrição principal sobre o que está em seu Test Drive. O cliente chegará aqui para ler quais cenários o seu test drive estará abordando sobre seu produto. 

**Manual do usuário-** *[campo obrigatório]* este é o passo a passo detalhado de sua experiência de teste de unidade. O cliente abrirá isso e poderá percorrer exatamente o que você deseja que eles façam em todo o seu Test Drive. É importante que esse conteúdo seja fácil de entender e seguir! (Deve ser um arquivo. pdf)

**Vídeo de demonstração do Test Drive-** \[recomendado\] semelhante ao manual do usuário, é melhor incluir um tutorial em vídeo de sua experiência de teste de unidade. O cliente observará isso antes ou durante o Test Drive e poderá percorrer exatamente o que você deseja que eles façam em seu Test Drive. É importante que esse conteúdo seja fácil de entender e seguir!

- **Nome** -título do seu vídeo
- **Link** -deve ser uma URL inserida do YouTube ou Vimeo. O exemplo de como obter a URL inserida está abaixo:
- **Miniatura** -deve ser uma imagem de alta qualidade (533x324) pixels. É recomendável tirar uma captura de tela de alguma parte da experiência do Test Drive aqui.

Veja abaixo como esses campos são mostrados para o cliente durante a experiência do Test Drive.

![Aparência dos campos do Test Drive](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração técnica

A próxima seção a ser preenchida é onde você configura o aplicativo lógico do Test Drive e define de que maneira especificamente suas instâncias de unidade de teste funcionam.

![Testar a configuração técnica](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Região** -  *[campo obrigatório]* a região selecionada é onde você escolhe onde os recursos do aplicativo lógico da unidade de teste são implantados.

    *Observação:* Se seu aplicativo lógico tiver recursos personalizados que são armazenados em uma região, verifique se a região está selecionada aqui. A melhor maneira de fazer isso é **implantar totalmente seu aplicativo lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona** antes de escrevê-lo aqui.

- **Máximo de unidades de teste simultâneas** -  *[campo obrigatório]* número de instâncias de unidade de teste que já estão implantadas e aguardando acesso por região selecionada. Os clientes podem acessar instantaneamente essas unidades de teste em vez de ter que esperar por uma implantação.

    *Observação:* Se você estiver executando uma webinar/classe em que deseja que todo o número de alunos de um deles faça um Test Drive, é recomendável publicar com N número de instâncias quentes e, depois, quando a classe estiver prestes a ser republicada para o seu número normal de instâncias ativas.

- **Duração da unidade de teste (horas) –** *[campo obrigatório]* duração por quanto tempo a unidade de teste permanecerá ativa, em \# de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure-** *[campo obrigatório]* escreva no nome do grupo de recursos em que as unidades de teste do aplicativo lógico são salvas.

- **Atribuir nome do aplicativo lógico-** *[campo obrigatório]* gravar no aplicativo lógico que é usado para atribuir um usuário na unidade de teste antes que o cliente o obtenha, grave o nome desse aplicativo lógico aqui. Verifique se esse arquivo foi salvo no grupo de recursos acima.

- **Desprovisionar nome do aplicativo lógico-** *[campo obrigatório]* escreva no nome do aplicativo lógico para o desprovisionamento de todos os recursos criados na unidade de teste. Verifique se esse arquivo foi salvo no grupo de recursos acima.

- **Informações de acesso-** *[campo obrigatório]* depois que um cliente obtém seu Test Drive, as informações de acesso são apresentadas a eles. Estas instruções destinam-se a compartilhar os parâmetros de saída úteis do modelo do Gerenciador de recursos de teste de unidade. Para incluir parâmetros de saída, use chaves duplas (por exemplo, **{{OutputType}}** ) e elas serão inseridas corretamente no local. (A formatação de cadeia de caracteres HTML é recomendada aqui para renderizar no front-end).

### <a name="test-drive-deployment-subscription-details"></a>Detalhes da assinatura de implantação do Test Drive

A seção final a ser preenchida é poder implantar as unidades de teste automaticamente conectando sua assinatura do Azure e Azure Active Directory (AD).

![Detalhes da assinatura de implantação do Test Drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID de assinatura do Azure** *[campo obrigatório]* isso concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Se você ainda não tiver uma assinatura **separada** do Azure para unidades de teste, vá em frente e crie uma. Você pode encontrar as IDs de assinatura do Azure fazendo logon no portal do Azure e navegando para as assinaturas no menu do lado esquerdo.
(Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subscrições do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de locatário do Azure ad** *[campo obrigatório]* se você já tiver uma ID de locatário disponível, poderá encontrá-la abaixo na ID de diretório de\> de propriedades.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo locatário no Azure Active Directory.

![Tela de propriedades de Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory] (./Media/Azure-Resource-Manager-Test-Drive/subdetails5.png)

![Azure Active Directory locatários](./media/azure-resource-manager-test-drive/subdetails6.png)

**Aplicativo Azure Ad ID** *[campo obrigatório]* a próxima etapa é criar e registrar um novo aplicativo. Usaremos esse aplicativo para executar operações em sua instância de unidade de teste.

1. Navegue até o diretório recém-criado ou o diretório já existente e selecione Azure Active Directory no painel de filtro.
2. Pesquise "Registros de aplicativo" e clique em "Adicionar"
3. Forneça um nome de aplicativo.
4. Selecione o tipo de "aplicativo Web/API"
5. Forneça qualquer valor na URL de logon, conquistamos\'t usando esse campo.
6. Clique em criar.
7. Depois que o aplicativo tiver sido criado, vá para propriedades-\> defina o aplicativo como multilocatário e clique em salvar.

Clique em Guardar. A última etapa é pegar a ID do aplicativo para esse aplicativo registrado e colá-lo no campo Test Drive aqui.

![ID do aplicativo Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Considerando que estamos usando o aplicativo para implantar na assinatura, precisamos adicionar o aplicativo como um colaborador na assinatura. As instruções para eles são as seguintes:

1. Navegue até a folha assinaturas e selecione a assinatura apropriada que você está usando somente para a unidade de teste.
1. Clique em **Controlo de acesso (IAM)** .
1. Clique na guia **atribuições de função** .  ![Azure Active Directory, adicionando uma nova entidade de segurança de controle de acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **Adicionar atribuição de função**.
1. Defina a função como **colaborador**.
1. Digite o nome do aplicativo do Azure AD e selecione o aplicativo para atribuir a função.
    ![Azure Active Directory permissões](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Guardar**.

**Chave de aplicativo Azure ad-** *[campo obrigatório]* o campo final é gerar uma chave de autenticação. Em chaves, adicione uma descrição da chave, defina a duração como nunca expirar e, em seguida, selecione salvar. É **importante** evitar ter uma chave expirada, o que interromperá sua Test Drive em produção. Copie esse valor e cole-o em seu campo Test Drive necessário.

![Azure Active Directory seção chaves](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Não é possível usar a visualização de registro Azure App porque ela não gera uma chave codificada em base64 no momento.


<a name="next-steps"></a>Passos seguintes
----------

Agora que você tem todos os campos de unidade de teste preenchidos, passe e **Republique** sua oferta. Depois que o test drive tiver passado o processo de certificação, você deverá seguir um teste extensivo da experiência do cliente na versão **prévia** da sua oferta. Inicie um test drive na interface do usuário e verifique se as unidades de teste estão sendo totalmente implantadas corretamente.

É importante observar que você não exclui nenhuma parte da unidade de teste à medida que elas são provisionadas para seus clientes, de modo que o serviço Test Drive limpará automaticamente esses grupos de recursos após a conclusão de um cliente.

Depois de se sentir confortável com sua oferta de visualização, agora é hora de **entrar em funcionamento**! Há um processo de revisão final da Microsoft depois que a oferta é publicada para verificar novamente a experiência completa de ponta a ponta. Se, por algum motivo, a oferta for rejeitada, enviaremos uma notificação para o contato de engenharia de sua oferta explicando o que será necessário para ser corrigido.

Se você tiver mais dúvidas, está procurando conselhos de solução de problemas ou deseja que seu teste seja mais bem-sucedido, acesse [perguntas frequentes, solução de problemas & práticas recomendadas](./marketing-and-best-practices.md).
