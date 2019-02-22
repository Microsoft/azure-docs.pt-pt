---
title: Verificação de recurso interativo de teste na validação de pilha do Azure como um serviço | Documentos da Microsoft
description: Saiba como criar testes de verificação de recurso interativo para o Azure Stack com validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20a48e67a24763af7bcce9e8831e2a1d1846d094
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594309"
---
# <a name="interactive-feature-verification-testing"></a>Teste de verificação de recurso interativo  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Pode utilizar a estrutura de testes de verificação de recurso interativo para pedir testes para o seu sistema. Quando solicita um teste, a Microsoft utiliza o framework para preparar os testes que requerem passos manuais de interativos. Microsoft pode utilizar a estrutura em conjunto encadear vários testes de autónomo automatizado.

Este artigo descreve um cenário simples de manual. O teste verifica a substituir um disco no Azure Stack. O framework recolhe registos de diagnóstico em cada passo. Pode depurar problemas à medida que os encontrar. A estrutura também permite a partilha dos registos produzidos por outras ferramentas ou processos e permite-lhe fornecer comentários sobre o cenário.

> [!Important]  
> Este artigo faz referência aos passos para realizar a identificação de disco. Isso é simplesmente uma demonstração, quaisquer resultados reunidos a partir do fluxo de trabalho de aprovação de teste não podem ser utilizados para verificar a solução nova.

## <a name="overview-of-interactive-testing"></a>Descrição geral de teste interativa

Um teste para a substituição do disco é um cenário comum. Neste exemplo, o teste tem sete etapas:

1. Criar uma nova **aprovação de teste** fluxo de trabalho
1. Selecione o **teste de identificação de disco**
1. Concluir o passo manual quando lhe for pedido
1. Verificar o resultado do cenário
1. Enviar o resultado do teste para a Microsoft

## <a name="create-a-new-test-pass"></a>Criar uma nova passagem de teste

Se não tiver um teste de existente passam disponíveis, siga as orientações para [um teste de agendamento](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Agendar o teste

1. Selecione **teste de identificação de disco**.

    > [!Note]  
    > A versão do teste irá incrementar quando são feitas melhorias para os acessórios de teste. Sempre deve ser utilizada a versão mais recente, a menos que a Microsoft contrária.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Forneça o nome de utilizador de administrador de domínio e palavra-passe, selecionando **editar**.

1. Selecione o teste apropriado execução agente/DVM para iniciar o teste.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Selecione **submeter** para iniciar o teste.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Acesso a interface do Usuário para o teste interativo do agente que selecionou no passo anterior.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Siga os **documentação** e **validação** links para rever as instruções da Microsoft sobre como efetuar este cenário.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Selecione **Seguinte**.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Siga as instruções para executar o script precheck.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Depois do script precheck foi concluída com êxito, execute o cenário manual (substituição do disco), de acordo a **documentação** e **validação** liga-se do **informações**separador.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Não feche a caixa de diálogo enquanto estiver a executar o cenário manual.

1. Quando tiver terminado de executar o cenário manual, siga as instruções para executar o script de verificação de post.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. A conclusão com êxito do cenário manual (substituição do disco), selecione **seguinte**.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Se fechar a janela, o teste irá parar antes de terminar.

1. Fornece feedback para a experiência de teste. Estas perguntas ajudará a avaliar a qualidade de taxa de e a versão de sucesso do cenário de Microsoft.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Anexe quaisquer ficheiros de registo que pretende enviar à Microsoft.

    ![Texto alternativo](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Aceite o EULA de envio de comentários.

1. Selecione **submeter** para enviar os resultados à Microsoft.

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)
