---
title: Certificar sua imagem de VM para o Azure Marketplace
description: Explica como testar e enviar uma imagem de VM para a certificação do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/26/2018
ms.author: pabutler
ms.openlocfilehash: 4088864db4bf861d07821f5a0287336d8431f889
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826511"
---
# <a name="certify-your-vm-image"></a>Certificar sua imagem de VM

Depois de criar e implantar sua VM (máquina virtual), você deve testar e enviar a imagem da VM para a certificação do Azure Marketplace. Este artigo explica onde obter a *ferramenta de teste de certificação para o Azure Certified*, como usar essa ferramenta para certificar sua imagem de VM e como carregar os resultados de verificação para o contêiner do Azure em que seus VHDs residem. 


## <a name="download-and-run-the-certification-test-tool"></a>Baixar e executar a ferramenta de teste de certificação

A ferramenta de teste de certificação para o certificado pelo Azure é executada em uma máquina local do Windows, mas testa uma VM do Windows ou Linux baseada no Azure.  Ele verifica se a imagem de VM do usuário é compatível com Microsoft Azure — que as diretrizes e os requisitos para preparar seu VHD foram atendidos. A saída da ferramenta é um relatório de compatibilidade, que você carregará no [portal do Cloud Partner](https://cloudpartner.azure.com) para solicitar a certificação de VM.

1. Baixe e instale a ferramenta de [teste de certificação mais recente para o Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Abra a ferramenta de certificação e clique em **Iniciar novo teste**.
3. Na tela **informações de teste** , insira um **nome de teste** para a execução de teste.
4. Selecione a **plataforma** para sua VM, seja `Windows Server` ou `Linux`. Sua escolha de plataforma afeta as opções restantes.
5. Se sua VM estiver usando esse serviço de banco de dados, marque a caixa de seleção **testar para o banco de dados SQL do Azure** .

   ![Página inicial da ferramenta de teste de certificado](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Conectar a ferramenta de certificação a uma imagem de VM

  A ferramenta se conecta a VMs baseadas no Windows com o [PowerShell](https://docs.microsoft.com/powershell/) e se conecta a VMs do Linux por meio do [SSH.net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Conectar a ferramenta de certificação a uma imagem de VM do Linux

1. Selecione o modo de **autenticação SSH** : `Password Authentication` ou `key File Authentication`.
2. Se estiver usando a autenticação baseada em senha, insira valores para o **nome DNS da VM**, o **nome de usuário**e a **senha**.  Opcionalmente, você pode alterar o número da **porta SSH** padrão.

     ![Autenticação de senha da imagem de VM do Linux](./media/publishvm_026.png)

3. Se estiver usando a chave de autenticação baseada em arquivo, insira valores para o **nome DNS da VM**, o **nome de usuário**e o local da **chave privada** .  Opcionalmente, você pode fornecer uma **frase secreta** ou alterar o número da **porta SSH** padrão.

     ![Autenticação de arquivo da imagem de VM do Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Conectar a ferramenta de certificação a uma imagem de VM baseada no Windows**
1. Insira o **nome DNS da VM** totalmente qualificado (por exemplo, `MyVMName.Cloudapp.net`).
2. Insira valores para o **nome de usuário** e a **senha**.

   ![Autenticação de senha da imagem da VM do Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Executar um teste de certificação

Depois de fornecer os valores de parâmetro para sua imagem de VM na ferramenta de certificação, selecione **testar conexão** para garantir uma conexão válida com sua VM. Depois que uma conexão for verificada, selecione **Avançar** para iniciar o teste.  Quando o teste for concluído, uma tabela será exibida com os resultados do teste (aprovação/falha/aviso).  O exemplo a seguir mostra os resultados de teste para um teste de VM do Linux. 

![Resultados do teste de certificação para imagem de VM do Linux](./media/publishvm_029.png)

Se qualquer um dos testes falhar, sua imagem *não* será certificada. Nesse caso, examine os requisitos e as mensagens de falha, faça as alterações indicadas e execute o teste novamente. 

Após o teste automatizado, é necessário fornecer informações adicionais sobre a imagem da VM na tela do **questionário** .  Ele contém duas guias que devem ser concluídas.  A guia **avaliação geral** contém perguntas **verdadeiro/falso** , enquanto a **personalização do kernel** contém várias perguntas de seleção e de forma livre.  Conclua as perguntas em ambas as guias e selecione **Avançar**.

![Questionário da ferramenta de certificação](./media/publishvm_030.png)

A última tela permite que você forneça informações adicionais, como informações de acesso SSH para uma imagem de VM do Linux e uma explicação para quaisquer avaliações com falha se você estiver buscando exceções. 

Por fim, clique em **gerar relatório** para baixar os resultados de teste e os arquivos de log para os casos de teste executados, além de suas respostas para o questionário. Salve os resultados no mesmo contêiner que os VHD.

![Salvar resultados do teste de certificação](./media/publishvm_031.png)


## <a name="next-steps"></a>Passos seguintes

Em seguida, você irá [gerar um URI (identificadores de recurso uniforme) para cada VHD](./cpp-get-sas-uri.md) que enviar ao Marketplace. 
