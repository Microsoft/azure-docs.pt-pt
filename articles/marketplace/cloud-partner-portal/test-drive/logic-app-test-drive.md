---
title: Unidade de Teste de Aplicações Lógicas [ Unidade de Teste de Aplicações Lógicas Mercado Azure
description: Explica como construir o seu Test Drive que se conecta com uma instância Dynamics AX/CRM ou qualquer outro recurso além do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278387"
---
<a name="logic-app-test-drive"></a>Unidade de teste de aplicativo lógico
====================

Este artigo é para editores que têm a sua oferta no AppSource e querem construir o seu Test Drive que se conecta com uma instância Dynamics AX/CRM ou qualquer outro recurso além do Apenas Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Como construir um Test Drive de Aplicações Lógicas
-----------------------------------

A documentação test drive para Unidades de Teste de Aplicações Lógicas ainda está no GitHub para [operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e envolvimento com o [cliente,](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)vá lá para ler mais.

<a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive
---------------------------

Agora que tem o test drive construído, esta secção percorre cada um dos campos necessários para que publique com sucesso o seu Test Drive.

![Ativar a função de unidade de teste](./media/azure-resource-manager-test-drive/howtopub1.png)

O primeiro e mais importante campo é alternar se deseja testar o formulário com todos os campos necessários para que possa preencher. Quando selecionar **Não,** o formulário fica desativado e se voltar a publicar com o Test Drive desativado, o test Drive é removido da produção.

*Nota:* Se existirem unidades de teste ativamente utilizadas pelos utilizadores, esses Test Drives continuarão a funcionar até que a sua sessão expire.

### <a name="details"></a>Detalhes

A próxima secção a preencher são os detalhes sobre a sua oferta test drive.

![Dados do condutor do teste](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição -** *[Campo Obrigatório]* É aqui que escreve a descrição principal sobre o que está no seu Test Drive. O cliente virá aqui para ler quais os cenários que o seu Test Drive irá cobrir sobre o seu produto. 

**Manual do utilizador -** *[Campo Obrigatório]* Esta é a passagem aprofundada da sua experiência test drive. O cliente abrirá isto e poderá passar exatamente pelo que pretende que eles façam durante o test drive. É importante que este conteúdo seja fácil de entender e acompanhar! (Deve ser um ficheiro .pdf)

**Test Drive Demo Video -** \[Recomendado\] Similar ao Manual do Utilizador, é melhor incluir um tutorial de vídeo da sua experiência test drive. O cliente irá assistir a isto antes ou durante o test drive e pode passar exatamente pelo que você quer que eles façam durante o seu Test Drive. É importante que este conteúdo seja fácil de entender e acompanhar!

- **Nome** - Título do seu Vídeo
- **Link** - Deve ser um URL incorporado do YouTube ou Vimeo. Exemplo sobre como obter o url incorporado está abaixo:
- **Miniatura** - Deve ser uma imagem de alta qualidade (533x324) pixels. Recomenda-se tirar uma imagem de alguma parte da sua experiência test drive aqui.

Abaixo está a forma como estes campos aparecem para o seu cliente durante a sua experiência test drive.

![Test Drive campos olhar e sentir](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração Técnica

A próxima secção a preencher é onde configura a sua Aplicação lógica test drive e define como as instâncias test drive funcionam especificamente.

![Configuração técnica test drive](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Região** - *[Campo Obrigatório]* A região que seleciona é onde escolhe onde os recursos da Aplicação Test Drive Logic são implantados.

    *Nota:* Se a sua App Lógica tiver algum recursos personalizados que estejam armazenados numa região, certifique-se de que a região é selecionada aqui. A melhor maneira de o fazer é implementar totalmente a **sua Aplicação Lógica localmente na sua subscrição Azure no portal e verificar se funciona** antes de a escrever aqui.

- **Unidades** - de teste simultâneas*máximas [Campo Necessário]* Número de instâncias de Test Drive que já estão implantadas e aguardam acesso por região selecionada. Os clientes podem aceder instantaneamente a este Test Drives em vez de terem de esperar por uma implementação.

    *Nota:* Se estiver a executar um webinar/classe onde pretende que todos os seus alunos do seu número N tomem um Test Drive, é aconselhável publicar com n número de instâncias Hot e, em seguida, uma vez que a aula acaba para republicar de volta ao seu número normal de casos Hot.

- **Duração do test drive (horas) -** *[Campo obrigatório]* Duração \# durante quanto tempo o Test Drive permanecerá ativo, em horas. O Test Drive termina automaticamente após o fim deste período de tempo.

- Nome do **Grupo de Recursos Azure -** *[Campo Obrigatório]* Escreva no nome do Grupo de Recursos onde os seus Test Drives de Aplicações Lógicas são guardados.

- Atribuir nome lógico **de aplicação -** *[Campo Obrigatório]* Escreva na Aplicação Lógica que é usada para atribuir um utilizador no Test Drive antes de o cliente o obter, escreva em nome dessa Aplicação Lógica aqui. Certifique-se de que este ficheiro é guardado no Grupo de Recursos acima.

- **Deprovisionamento Lógica Nome de aplicação -** *[Campo Obrigatório]* Escreva no nome da App Lógica para a sua desprovisionamento de todos os recursos criados na Test Drive. Certifique-se de que este ficheiro é guardado no Grupo de Recursos acima.

- **Informação de acesso -** *[Campo Obrigatório]* Depois de um cliente obter o seu Test Drive, a informação de acesso é-lhes apresentada. Estas instruções destinam-se a partilhar os parâmetros de saída úteis do seu modelo de Gestor de Recursos test drive. Para incluir parâmetros de saída, utilize suportes duplos encaracolados (por exemplo, **{{outputname}}**), e serão inseridos corretamente no local. (A formatação de cordas HTML é recomendada aqui para renderizar na extremidade frontal).

### <a name="test-drive-deployment-subscription-details"></a>Detalhes da subscrição de implementação de unidade de teste

A secção final a preencher é poder implementar automaticamente as Unidades de Teste ligando a sua Subscrição Azure e o Diretório Ativo Azure (AD).

![Detalhes de subscrição de implementação test drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID de subscrição Azure** *[Campo Obrigatório]* Este dá acesso aos serviços Azure e ao portal Azure. A subscrição é onde o uso de recursos é reportado e os serviços são cobrados. Se ainda não tiver uma subscrição **Azure separada** apenas para Test Drives, por favor, vá em frente e faça um. Pode encontrar Ids de subscrição azure iniciando sessão no portal Azure e navegando para as Subscrições no menu do lado esquerdo.
(Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subscrições do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

Id do **Inquilino Azure AD** *[Campo Obrigatório]* Se tiver um ID de\> Inquilino já disponível, pode encontrá-lo abaixo no ID de Diretório .

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo Inquilino no Diretório Ativo Azure.

![Ecrã de propriedades de diretório ativo Azure](./media/azure-resource-manager-test-drive/subdetails4.png)

! Diretório Ativo Azure](./media/azure-resource-manager-test-drive/subdetails5.png)

![Inquilinos do Diretório Ativo Azure](./media/azure-resource-manager-test-drive/subdetails6.png)

Id da **aplicação Azure AD** *[Campo Obrigatório]* Próximo passo é criar e registar uma nova aplicação. Utilizaremos esta aplicação para efetuar operações na sua instância test drive.

1. Navegue para o diretório recém-criado ou já existente e selecione o diretório Azure Ative no painel de filtros.
2. Pesquise "Registos de aplicações" e clique em "Adicionar"
3. Forneça um nome de candidatura.
4. Selecione o tipo de "Web app / API"
5. Fornecer qualquer valor em URL de\'inscrição, não usaremos esse campo.
6. Clique em criar.
7. Depois de criada a aplicação,\> vá ao Properties - Desloque a aplicação como multi-inquilino e atinja save.

Clique em Guardar. O último passo é pegar no ID da aplicação para esta aplicação registada e colá-lo no campo Test Drive aqui.

![ID de aplicação de diretório ativo Azure](./media/azure-resource-manager-test-drive/subdetails7.png)

Dado que estamos a usar a aplicação para implementar a subscrição, precisamos adicionar a aplicação como contribuinte na subscrição. As instruções para estas são as seguintes:

1. Navegue na lâmina de Subscrições e selecione a subscrição apropriada que está a utilizar apenas para o Test Drive.
1. Clique em **Controlo de acesso (IAM)**.
1. Clique no separador de **atribuições de role.**  ![Diretório Azure Ative, adicionando um novo diretor de Controlo de Acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **Adicionar a atribuição de funções**.
1. Desinuque o papel de **Contribuinte.**
1. Digite em nome da aplicação Azure AD e selecione a aplicação para atribuir a função.
    ![Permissões de Diretório Ativo Azure](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Guardar**.

**Chave de aplicações Azure AD -** *[Campo Obrigatório]* O campo final é gerar uma chave de autenticação. Em baixo das teclas, adicione uma Descrição chave, detete a duração para nunca expirar e, em seguida, selecione guardar. É **importante** evitar ter uma chave caducada, que irá quebrar o seu test drive em produção. Copie este valor e cole-o no campo de Test Drive necessário.

![Secção Azure Ative Directory Keys](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Não é possível utilizar a pré-visualização de registo de aplicações Azure porque não gera atualmente uma chave codificada base64.


<a name="next-steps"></a>Passos seguintes
----------

Agora que tem todos os campos de Test Drive preenchidos, vá e **republique** a sua oferta. Uma vez que o seu Test Drive tenha passado o processo de certificação, deverá testar extensivamente a experiência do cliente na **pré-visualização** da sua oferta. Inicie uma Unidade de Teste na UI e verifique se as suas Unidades de Teste estão a ser totalmente implantadas corretamente.

É importante notar que não elimina nenhuma parte da Test Drive, uma vez que são provisionadas para os seus clientes, pelo que o serviço Test Drive irá limpar automaticamente estes Grupos de Recursos depois de um cliente terminar com o mesmo.

Uma vez que se sinta confortável com a sua oferta de Pré-visualização, agora é hora de **ir ao vivo!** Existe um processo final de revisão da Microsoft uma vez que a oferta foi publicada para verificar o final da experiência final. Se por alguma razão a oferta for rejeitada, enviaremos uma notificação ao contacto de engenharia para a sua oferta explicando o que terá de ser corrigido.

Se tiver mais perguntas, estiver à procura de conselhos de resolução de problemas, ou queira tornar o seu Test Drive mais bem sucedido, por favor vá ao [FAQ,Troubleshooting, & Melhores Práticas.](./marketing-and-best-practices.md)
