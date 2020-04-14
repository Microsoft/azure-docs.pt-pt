---
title: Certifique a sua imagem VM para o Mercado Azure
description: Explica como testar e submeter uma imagem VM para certificação Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: ce1e001b9cafff83a3f9bf546d6903cc4a4f450f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273499"
---
# <a name="certify-your-vm-image"></a>Certifica a sua imagem VM

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na certificação de [imagem Azure VM](https://aks.ms/CertifyVMimage) para gerir as suas ofertas migratórias.

Depois de criar e implementar a sua máquina virtual (VM), tem de testar e submeter a imagem VM para a certificação Azure Marketplace. Este artigo explica onde obter a Ferramenta de Teste de *Certificação para certificação Azure Certified,* como usar esta ferramenta para certificar a sua imagem VM, e como enviar os resultados de verificação para o recipiente Azure onde residem os seus VHDs. 


## <a name="download-and-run-the-certification-test-tool"></a>Descarregue e execute a ferramenta de teste de certificação

A ferramenta de teste de certificação da Azure Certified funciona numa máquina local do Windows, mas testa um Windows ou VM Linux baseados em Azure.  Verifica que a imagem VM do utilizador é compatível com o Microsoft Azure — que as orientações e requisitos em torno da preparação do seu VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade, que irá enviar para o [Portal do Parceiro cloud](https://cloudpartner.azure.com) para solicitar a certificação VM.

1. Descarregue e instale a mais recente ferramenta de teste de [certificação para a Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Abra a ferramenta de certificação e, em seguida, clique em **Iniciar Novo Teste**.
3. A partir do ecrã de informações de **teste,** introduza um nome de **teste** para o ensaio.
4. Selecione a **Plataforma** para `Windows Server` `Linux`o seu VM, ou . A escolha da sua plataforma afeta as opções restantes.
5. Se o seu VM estiver a utilizar este serviço de base de dados, selecione o Teste para a Caixa de Dados **Azure SQL.**

   ![Página inicial da ferramenta de teste cert](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Ligue a ferramenta de certificação a uma imagem VM

  A ferramenta liga-se a VMs baseados no Windows com [powerShell](https://docs.microsoft.com/powershell/) e conecta-se a VMs Linux [através de SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Ligue a ferramenta de certificação a uma imagem De Linux VM

1. Selecione o modo de `Password Authentication` `key File Authentication` **autenticação SSH:** ou .
2. Se utilizar a autenticação baseada em palavra-passe, introduza valores para o **nome DNS VM,** nome do **utilizador**, e **palavra-passe**.  Opcionalmente, pode alterar o número de **porta SSH** predefinido.

     ![Autenticação de palavra-passe da Imagem VM do Linux](./media/publishvm_026.png)

3. Se utilizar a autenticação baseada em ficheiros chave, introduza valores para o **Nome DNS VM,** **nome do utilizador**e localização da chave **privada.**  Opcionalmente, pode fornecer uma **palavra-passe** ou alterar o número padrão **da porta SSH.**

     ![Autenticação de ficheiros da Imagem VM do Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Ligue a ferramenta de certificação a uma imagem VM baseada no Windows**
1. Introduza o **nome DNS VM** `MyVMName.Cloudapp.net`totalmente qualificado (por exemplo, ).
2. Introduza valores para o Nome de **Utilizador** e **palavra-passe**.

   ![Autenticação de palavra-passe da Imagem VM do Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Realizar um teste de certificação

Depois de ter fornecido os valores do parâmetro para a sua imagem VM na ferramenta de certificação, selecione **Test Connection** para garantir uma ligação válida ao seu VM. Depois de verificada uma ligação, selecione **Next** para iniciar o teste.  Quando o teste estiver concluído, é apresentada uma tabela com os resultados do teste (Passe/Falha/Aviso).  O exemplo seguinte mostra os resultados dos testes para um teste De VM Linux. 

![Resultados dos testes de certificação para imagem De Linux VM](./media/publishvm_029.png)

Se algum dos testes falhar, a sua imagem *não* está certificada. Neste caso, reveja os requisitos e as mensagens de falha, efaça as alterações indicadas e refaça o teste. 

Após o teste automatizado, é-lhe exigido que forneça informações adicionais sobre a sua imagem VM no ecrã **do Questionário.**  Contém dois separadores que deve completar.  O separador **De Avaliação Geral** contém perguntas **verdadeiras/falsas,** enquanto a **Personalização Kernel** contém múltiplas questões de seleção e forma livre.  Complete as perguntas em ambos os separadores e, em seguida, selecione **Seguinte**.

![Questionário de Ferramentas de Certificação](./media/publishvm_030.png)

O último ecrã permite-lhe fornecer informações adicionais, tais como informações de acesso SSH para uma imagem De VM Linux e uma explicação para quaisquer avaliações falhadas se estiver à procura de exceções. 

Por último, clique em **Generate Report** para descarregar os resultados dos testes e registar ficheiros para os casos de teste executados, além das suas respostas ao questionário. Guarde os resultados no mesmo recipiente que o seu VHD(s).

![Salvar os resultados dos testes de certificação](./media/publishvm_031.png)


## <a name="next-steps"></a>Passos seguintes

Em seguida, você [gerará um identificador de recursos uniformes (URI) para cada VHD](./cpp-get-sas-uri.md) que você submete ao mercado. 
