---
title: Programa Azure Certified Device - Tutorial - Criação do seu projeto
description: Guia para criar um projeto no portal Azure Certified Device
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: e5602e133ac8ab13779c9dfebca21d97265d9d76
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969809"
---
# <a name="tutorial-create-your-project"></a>Tutorial: Crie o seu projeto

Parabéns por ter escolhido certificar o seu dispositivo através do programa Azure Certified Device! Já selecionou o programa de certificação apropriado para o seu dispositivo e está pronto para começar no portal.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Inscreva-se no portal do [dispositivo certificado Azure](https://certify.azure.com/)
> * Crie um novo projeto de certificação para o seu dispositivo
> * Especifique detalhes básicos do dispositivo do seu projeto

## <a name="prerequisites"></a>Pré-requisitos

- Você precisará de uma conta de trabalho/escola [Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)válida .
- Vai precisar de uma conta verificada na Microsoft Partner Network (MPN). Se não tiver uma conta MPN, [junte-se à rede de parceiros](https://partner.microsoft.com/) antes de começar.

## <a name="signing-into-the-azure-certified-device-portal"></a>Iniciar sessão no portal do dispositivo certificado Azure

Para começar, tem de iniciar sedível no portal, onde irá fornecer informações sobre o seu dispositivo, completar os testes de certificação e gerir as publicações do seu dispositivo no catálogo de Dispositivos Certificados Azure.

1. Aceda ao [portal do Dispositivo Certificado Azure](https://certify.azure.com).
1. Selecione `Company profile` no lado esquerdo e atualize as informações do fabricante.
   ![Seção de perfil da empresa](./media/images/company-profile.png)
1. Aceite o acordo de programa para iniciar o seu projeto.

## <a name="creating-your-project-on-the-portal"></a>Criar o seu projeto no portal

Agora que estão todos configurado no portal, podem iniciar o processo de certificação. Primeiro, tem de criar um projeto para o seu dispositivo.

1. No ecrã principal, selecione `Create new project` . Isto abrirá uma janela para adicionar informações básicas do dispositivo na secção seguinte.

 ![Imagem do novo botão de projeto Criar](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Identificar informações básicas do dispositivo

Em seguida, deve fornecer informações básicas do dispositivo. Pode editar esta informação mais tarde.

1. Complete os campos solicitados na `Basics` secção. Consulte o quadro abaixo para obter esclarecimentos sobre os campos **necessários:**

    | Campos                  | Description                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Nome do projeto           | Nome interno que não será visível no catálogo de Dispositivos Certificados Azure                                                        |
    | Nome do dispositivo            | Nome público para o seu dispositivo                                                                                                |
    | Tipo de Dispositivo            | Especificação do produto acabado ou Solution-Ready Kit de Desenvolvimento.     Para obter mais informações sobre a terminologia, consulte [o glossário de certificação.](./resources-glossary.md)                                                                     |
    | Classe de dispositivo           | Gateway, Sensor, ou outros.  Para obter mais informações sobre a terminologia, consulte [o glossário de certificação.](./resources-glossary.md)                                                                    |
    | URL de código fonte do dispositivo | Obrigatório se estiver a certificar um dev kit de Solution-Ready, opcional de outra forma. O URL deve ser para uma localização GitHub para o seu código de dispositivo. |
1. Selecione o `Next` botão para continuar a fazer o `Certifications` separador.

    ![Imagem do novo formulário de projeto Criar, Separador de Certificações](./media/images/create-new-project-certificationswindow.png)

1. Especifique quais as certificações que pretende obter para o seu dispositivo.
1. Selecione `Create` e o novo projeto será guardado e visível na página inicial do portal.

    ![Imagem da tabela do projeto](./media/images/project-table.png)

1. Selecione o nome do Projeto na tabela. Isto irá lançar a página de resumo do projeto onde pode adicionar e ver outros detalhes sobre o seu dispositivo.

    ![Imagem da página de detalhes do projeto](./media/images/device-details-section.png)

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para adicionar detalhes do dispositivo e testar o seu dispositivo utilizando o nosso serviço de certificação. Avance para o próximo artigo para saber como editar os detalhes do seu dispositivo.
> [!div class="nextstepaction"]
> [Tutorial: Adicionar detalhes do dispositivo](tutorial-02-adding-device-details.md)
