---
title: Unidade de teste da aplicação lógica | Documentos da Microsoft
description: Explica como criar a unidade de teste que se liga com uma instância do Dynamics AX/CRM ou qualquer outro recurso além do Azure apenas.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: d784941c43da13a2c1bd120599aa02fe14e5a5b4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456826"
---
<a name="logic-app-test-drive"></a>Unidade de teste da aplicação lógica
====================

Este artigo é para os publicadores que tem suas ofertas no AppSource e pretendem criar a unidade de teste que se liga com uma instância do Dynamics AX/CRM ou qualquer outro recurso além do Azure apenas.

<a name="how-to-build-a-logic-app-test-drive"></a>Como criar uma unidade de teste da aplicação lógica
-----------------------------------

Testar a unidade de documentação para versões de teste de aplicação lógica é, atualmente, ainda no GitHub para [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app), aceda aqui para ler mais.

<a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive
---------------------------

Agora que tem a sua versão de teste criada, esta secção descreve cada um dos campos necessários para a publicação com êxito a sua versão de teste.

![Ativar a funcionalidade de Test-Drive](./media/azure-resource-manager-test-drive/howtopub1.png)

O campo de primeiro e mais importante é ativar/desativar se pretende que o teste do formulário com todos os campos obrigatórios são apresentadas para que possa preencher. Quando seleciona **não,** o formulário torna-se desativado e se voltar a publicar com o Test-Drive desativada, a sua versão de teste é removido da produção.

*Nota*: Se houver quaisquer versões de teste ativamente utilizadas por utilizadores, essas versões de teste irá continuar a ser executado até a sessão expira.

### <a name="details"></a>Detalhes

A secção seguinte para preencher é que oferecem os detalhes sobre a sua versão de teste.

![Detalhes do controlador de teste](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição -** *[necessário campo]* é onde escrever a descrição principal sobre o que há em sua versão de teste. O cliente virá aqui para ler quais cenários sua versão de teste irá abordar sobre seu produto. 

**Manual do usuário -** *[necessário campo]* este é o passo a passo detalhada de sua experiência de versão de teste. O cliente abrirá isso e pode percorrer exatamente o que deseja fazer em toda a unidade de teste. É importante que este conteúdo é fácil de compreender e siga! (Tem de ser um ficheiro. pdf)

**Testar o vídeo de demonstração de unidade -** \[recomendado\] é semelhante para o Manual do utilizador, é melhor incluir um tutorial em vídeo de sua experiência de versão de teste. O cliente irá observar neste anterior ou durante a sua versão de teste e pode percorrer exatamente o que deseja fazer em toda a unidade de teste. É importante que este conteúdo é fácil de compreender e siga!

- **Nome** -título do seu vídeo
- **Ligação** -tem de ser um URL incorporado do YouTube ou Vimeo. Exemplo sobre como obter o url do embedded está ilustrado abaixo:
- **Miniatura** -tem de ser uma imagem de alta qualidade pixels (533 x 324). Recomenda-se para tirar uma captura de ecrã de certa parte de sua experiência de Test-Drive aqui.

Segue-se como estes campos aparecem para o seu cliente durante a sua experiência de versão de teste.

![Teste de unidade campos aspeto e funcionalidade](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração técnica

A secção seguinte para preencher é onde configurar a aplicação de lógica de unidade de teste e define especificamente como sua versão de teste instâncias de trabalho.

![Configuração de técnica de unidade de teste](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Região** - *[Field necessário]* a região que selecionou é onde escolhe em que os recursos de teste de unidade lógica aplicação são implementados no.

    *Nota:* Se a sua aplicação lógica tiver todos os recursos personalizados que estão armazenados numa região, certificar-se de que essa região está selecionada aqui. É a melhor forma de fazer isso **totalmente implementar a sua aplicação lógica localmente na sua subscrição do Azure no portal e certifique-se de que ele funciona** antes de os escrever aqui.

- **Máximo versões de teste de simultâneas** - *[Field necessário]* aceder a instâncias de número de teste de unidade de mensagens em fila que já estão implementados e espera por região selecionada. Os clientes podem aceder de forma instantânea este versões de teste, em vez de ter de esperar por uma implementação.

    *Nota:* Se estiver a executar uma webinar/classe onde pretende que todos os seu número de N de estudantes para fazer um Test Drive, recomenda-se para publicar com N diversas instâncias de acesso frequente e uma vez, em seguida, a classe está a terminar, voltar a publicar novamente para o seu número normal de instâncias de acesso frequente.

- **Teste de unidade de duração (horas) -** *[necessário Field]* duração para o tempo que o Test-Drive permanecerá ativo, em \# de horas. O Test-Drive automaticamente termina após o final deste período de tempo.

- **Nome do grupo de recursos do Azure -** *[necessário campo]* escrita no nome do grupo de recursos em que as versões de teste de lógica de aplicação são guardadas.

- **Atribuir o nome da aplicação lógica -** *[necessário campo]* escrita na lógica de aplicação que é utilizada para atribuir um utilizador na unidade de teste antes do cliente recebe, escrever no nome da aplicação lógica aqui. Certifique-se de que o ficheiro é guardado no grupo de recursos acima.

- **Desaprovisionar o nome da aplicação lógica -** *[necessário campo]* escrever o nome de aplicação lógica para o desaprovisionamento de todos os recursos criados na unidade de teste. Certifique-se de que o ficheiro é guardado no grupo de recursos acima.

- **Aceder a informações -** *[necessário campo]* depois de um cliente recebe a unidade de teste, as informações de acesso são apresentadas aos mesmos. Estas instruções destinam-se para partilhar os parâmetros de saída útil do seu teste de unidade modelo do Resource Manager. Para incluir parâmetros de saída, de utilizar chavetas aspas (por exemplo, **{{outputname}}**), e eles serão inseridos corretamente na localização. (Formatação da cadeia de caracteres HTML é recomendado aqui para renderizar no front-end).

### <a name="test-drive-deployment-subscription-details"></a>Detalhes de subscrição de implementação de unidade de teste

A seção final para preencher é ser capaz de implantar as versões de teste automaticamente ao ligar-se a sua subscrição do Azure e o Azure Active Directory (AD).

![Detalhes da subscrição de unidade de implementação de teste](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID de subscrição do Azure** *[necessário Field]* isso concede acesso a serviços do Azure e o portal do Azure. A subscrição está em que é comunicada a utilização de recursos e serviços são cobrados. Se ainda não tiver uma **separado** subscrição do Azure para versões de teste apenas,. Vá em frente e criar uma. Pode encontrar os Ids de subscrição do Azure iniciando sessão no portal do Azure e navegando para as subscrições no menu da esquerda.
(Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subscrições do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de inquilino do Azure AD** *[necessário Field]* se tiver um ID de inquilino já disponível que pode encontrá-lo abaixo nas propriedades -\> ID do diretório.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo inquilino no Azure Active Directory.

![Ecrã de propriedades do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Directory](./media/azure-resource-manager-test-drive/subdetails5.png) Active Directory do Azure

![Inquilinos do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID de aplicação do Azure AD** *[necessário Field]* próxima etapa é criar e registar uma nova aplicação. Utilizaremos esta aplicação para realizar operações na sua instância de teste de unidade.

1. Navegue para o diretório recentemente criado ou já existente diretório e selecione o Azure Active directory no painel de filtro.
2. Procure "Registos de aplicações" e clique em "Adicionar"
3. Forneça um nome de aplicação.
4. Selecione o tipo de como "aplicação Web / API"
5. Forneça qualquer valor no URL de início de sessão, ganhamos\'t estar a utilizar esse campo.
6. Clique em criar.
7. Depois do aplicativo tiver sido criado, vá para propriedades -\> definir a aplicação como multi-inquilino e clique em Save.

Clique em Guardar. A última etapa é obter o ID da aplicação para esta aplicação registada e cole-o no campo da versão de teste aqui.

![ID da aplicação do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Tendo em conta que estiver a utilizar a aplicação para implementar para a subscrição, precisamos de adicionar a aplicação como um contribuinte da subscrição. As instruções para estas são como abaixo:

1. Navegue para o painel de subscrições e selecione a subscrição adequada, que está a utilizar para a versão de teste apenas.
1. Clique em **controlo de acesso (IAM)**.
1. Clique nas **atribuições de funções** separador.  ![O Azure Active Directory, adicionar um novo principal de controlo de acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **adicionar atribuição de função**.
1. Definir a função como **contribuinte**.
1. Escreva o nome de aplicação do Azure AD e selecione a aplicação para atribuir a função.
    ![Permissões do Active Directory do Azure](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Guardar**.

**Chave da aplicação do Azure AD -** *[necessário campo]* o campo final é gerar uma chave de autenticação. Em chaves, adicione uma descrição de chave, defina a duração para nunca expirar, em seguida, selecione guardar. É **importante** para evitar que um expiradas chave, que irá interromper a sua versão de teste em produção. Copie este valor e cole-o no seu campo obrigatório da versão de teste.

![Secção de chaves de diretório Active Directory do Azure](./media/azure-resource-manager-test-drive/subdetails8.png)

<a name="next-steps"></a>Passos Seguintes
----------

Agora que tem todos os campos de Test-Drive preenchidos, passar por e **voltar a publicar** sua oferta. Assim que a sua versão de teste tiver passado o processo de certificação, deve passar um extensivamente testar a experiência do cliente no **pré-visualização** da sua oferta. Iniciar uma versão de teste na interface de Usuário e certifique-se de que as versões de teste estão a ser totalmente implementados corretamente.

É importante observar que não excluir qualquer parte da unidade de teste como terem sido aprovisionados para os seus clientes para que o serviço de versão de teste irá limpar automaticamente estes grupos de recursos depois de um cliente tenha terminado com ele.

Assim que se sentir à vontade com a sua oferta de pré-visualização, agora é hora de **implemente**! Há um processo de revisão final da Microsoft, assim que a oferta foi publicada duplique verificação toda a experiência de ponta a ponta. Se por algum motivo, a oferta é rejeitada, enviaremos uma notificação para o contacto de engenharia da sua oferta explicando o que será necessário obter corrigidos.

Se tiver mais perguntas, estiver à procura de conselhos de resolução de problemas ou pretende tornar a sua versão de teste mais bem-sucedida, vá até [FAQ, resolução de problemas e melhores práticas](./marketing-and-best-practices.md).
