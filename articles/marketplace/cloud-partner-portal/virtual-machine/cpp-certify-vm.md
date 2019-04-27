---
title: Certificar a sua imagem VM para o Azure Marketplace | Documentos da Microsoft
description: Explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace.
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
ms.topic: article
ms.date: 09/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 24430b1b785a24da06a8ea51594147040e6d5bd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638398"
---
# <a name="certify-your-vm-image"></a>Certificar a sua imagem VM

Depois de criar e implementar a sua máquina virtual (VM), tem de testar e enviar a imagem de VM para a certificação do Azure Marketplace. Este artigo explica onde obter o *ferramenta de teste de certificação para o Azure Certified*, como utilizar esta ferramenta para certificar a sua imagem VM e como carregar os resultados de verificação para o contentor do Azure onde residem os VHDs. 


## <a name="download-and-run-the-certification-test-tool"></a>Transfira e execute a ferramenta de teste de certificação

A ferramenta de teste de certificação para o Azure Certified é executado num computador Windows local, mas testes de um Windows baseadas no Azure ou de uma VM do Linux.  Verifica se a sua imagem VM do utilizador é compatível com o Microsoft Azure — se a documentação de orientação e aos requisitos de preparação do VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade, que irá carregar para o [Cloud Partner Portal](https://cloudpartner.azure.com) para solicitar a certificação de VM.

1. Transfira e instale o mais recente [ferramenta de teste de certificação para o Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Abra a ferramenta de certificação e, em seguida, clique em **Start New Test**.
3. Do **Test Information** ecrã, introduza um **testar nome** para a execução do teste.
4. Selecione o **plataforma** para a sua VM, seja `Windows Server` ou `Linux`. Sua escolha de plataforma afeta as opções restantes.
5. Se a VM estiver a utilizar este serviço de base de dados, selecione o **teste para a base de dados do Azure SQL** caixa de verificação.

   ![Página inicial do ferramenta de teste de CERT](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Ligar a ferramenta de certificação a uma imagem de VM

  A ferramenta se conectar a VMs com base no Windows com [PowerShell](https://docs.microsoft.com/powershell/) e liga-se para VMs do Linux através do [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Ligar a ferramenta de certificação a uma imagem de VM do Linux

1. Selecione o **autenticação SSH** modo: `Password Authentication` ou `key File Authentication`.
2. Se utilizar autenticação baseada em palavra-passe, introduza os valores para o **nome de DNS de VM**, **nome de utilizador**, e **palavra-passe**.  Opcionalmente, pode alterar a predefinição **porta SSH** número.

     ![Autenticação de palavra-passe da imagem de VM do Linux](./media/publishvm_026.png)

3. Se utilizar a autenticação baseada em ficheiros chave, introduza os valores para o **nome de DNS de VM**, **nome de utilizador**, e **chave privada** localização.  Opcionalmente, pode fornecer uma **frase de acesso** ou alterar a predefinição **porta SSH** número.

     ![Autenticação de ficheiro de imagem de VM do Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ligar a ferramenta de certificação a uma imagem VM baseada no Windows**
1. Introduza o completamente qualificado **nome de DNS de VM** (por exemplo, `MyVMName.Cloudapp.net`).
2. Introduza os valores para o **nome de utilizador** e **palavra-passe**.

   ![Autenticação de palavra-passe da imagem de VM do Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Executar um teste de certificação

Depois que forneceu os valores de parâmetro para que a imagem VM na ferramenta de certificação, selecione **Testar ligação** para garantir uma ligação válida para a VM. Depois de verificar uma ligação, selecione **seguinte** para iniciar o teste.  Quando o teste estiver concluído, uma tabela é apresentada com os resultados do teste (Pass/Fail/Warning).  O exemplo seguinte mostra os resultados do teste para um teste de VM do Linux. 

![Resultados do teste de certificação de imagem de VM do Linux](./media/publishvm_029.png)

Se algum dos testes falharem, a sua imagem está *não* certified. Neste caso, reveja os requisitos e as mensagens de falha, faça as alterações indicadas e volte a executar o teste. 

Após o teste automatizado, tem de fornecer informações adicionais sobre a imagem de VM sobre o **questionário** ecrã.  Ele contém dois separadores que tem de concluir.  O **avaliação geral** separador contém **True/False** perguntas, ao passo que o **personalização de Kernel** contém vários seleção e perguntas de forma livre.  Conclua as perguntas em ambos os separadores, em seguida, selecione **seguinte**.

![Questionário de ferramenta de certificação](./media/publishvm_030.png)

O último ecrã permite-lhe fornecer informações adicionais, como informações de acesso SSH para uma imagem de VM do Linux e uma explicação para qualquer avaliações com falha se necessita de exceções. 

Por último, clique em **gerar relatório** para transferir os resultados do teste e ficheiros de registo para os casos de teste executados além do mais para as suas respostas ao questionário. Guarde os resultados no mesmo contentor como seu VHD.

![Guardar os resultados do teste de certificação](./media/publishvm_031.png)


## <a name="next-steps"></a>Passos Seguintes

Em seguida, irá [gerar uma identificadores de recurso uniforme (URI) para cada VHD](./cpp-get-sas-uri.md) que submeter no Marketplace. 
