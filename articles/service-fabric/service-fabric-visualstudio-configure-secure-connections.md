---
title: Configure ligações de cluster de tecido de serviço azure seguros
description: Aprenda a usar o Visual Studio para configurar ligações seguras que são suportadas pelo cluster Azure Service Fabric.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75464088"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configure ligações seguras a um cluster de tecido de serviço do Estúdio Visual
Aprenda a usar o Visual Studio para aceder de forma segura a um cluster Azure Service Fabric com políticas de controlo de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de ligação de cluster
Dois tipos de ligações são suportadas pelo cluster Azure Service Fabric: ligações **não seguras** e ligações seguras **baseadas em certificadox509.** (Para clusters de tecido de serviço hospedados no local, **windows** e autenticações **dSTS** também são suportados.) Tem de configurar o tipo de ligação do cluster quando o cluster está a ser criado. Uma vez criado, o tipo de ligação não pode ser alterado.

As ferramentas Visual Studio Service Fabric suportam todos os tipos de autenticação para a ligação a um cluster para publicação. Consulte a [instalação de um cluster de tecido](service-fabric-cluster-creation-via-portal.md) de serviço do portal Azure para obter instruções sobre como configurar um cluster de tecido de serviço seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurar ligações de cluster em perfis de publicação
Se publicar um projeto de Tecido de Serviço do Estúdio Visual, utilize a caixa de diálogo **Publish Service Fabric Application** para escolher um cluster Azure Service Fabric. No ponto **final da Ligação,** selecione um cluster existente sob a sua subscrição.

![A caixa de diálogo **Publish Fabric Application** é utilizada para configurar uma ligação de tecido de serviço.][publishdialog]

A caixa de diálogo De publicação de **aplicação** de tecido de tecido valida automaticamente a ligação do cluster. Se solicitado, inscreva-se na sua conta Azure. Se a validação passar, significa que o seu sistema tem os certificados corretos instalados para ligar ao cluster de forma segura, ou o seu cluster não está seguro. As falhas de validação podem ser causadas por problemas de rede ou por não ter o seu sistema corretamente configurado para se ligar a um cluster seguro.

![A caixa de diálogo **Publish Fabric Application** valida uma ligação de cluster de tecido de serviço existente e corretamente configurada.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para ligar a um cluster seguro
1. Certifique-se de que pode aceder a um dos certificados de cliente em que o cluster de destino confia. O certificado é geralmente partilhado como um ficheiro Personal Information Exchange (.pfx). Consulte a [criação de um cluster de Tecido de Serviço a partir do portal Azure](service-fabric-cluster-creation-via-portal.md) para configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado de confiança. Para tal, clique duas vezes no ficheiro .pfx ou utilize o script PowerShell Import-PfxCertificate para importar os certificados. Instale o certificado na **Cert:\LocalMachine\My**. Não há problema em aceitar todas as definições predefinidas enquanto importa o certificado.
3. Escolha a **Publicação...** comando no menu de atalho do projeto para abrir a caixa de diálogo **Publish Azure Application** e, em seguida, selecione o cluster alvo. A ferramenta resolve automaticamente a ligação e guarda os parâmetros de ligação seguros no perfil de publicação.
4. Opcional: Pode editar o perfil de publicação para especificar uma ligação de cluster segura.
   
   Uma vez que está a editar manualmente o ficheiro Publish Profile XML para especificar as informações do certificado, certifique-se de notar o nome da loja de certificados, a localização da loja e a impressão digital do certificado. Terá de fornecer estes valores para o nome da loja e loja do certificado. Ver [Como: Recuperar a impressão digital de um Certificado](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) para obter mais informações.
   
   Pode utilizar os parâmetros *ClusterConnectionParameters* para especificar os parâmetros PowerShell a utilizar quando ligar ao cluster De serviço. Os parâmetros válidos são os que são aceites pelo cmdlet Connect-ServiceFabricCluster. Consulte o [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) para obter uma lista de parâmetros disponíveis.
   
   Se estiver a publicar num cluster remoto, precisa especificar os parâmetros adequados para esse cluster específico. Segue-se um exemplo de ligação a um cluster não seguro:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Aqui está um exemplo para ligar a um cluster seguro baseado em certificadox509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Editar quaisquer outras definições necessárias, tais como parâmetros de atualização e localização de ficheiros paramétricos de aplicação, e depois publicar a sua aplicação na caixa de diálogo De publicação de Aplicação de **Tecidos** de Serviço no Estúdio Visual.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o acesso aos clusters de tecido de serviço, consulte [visualizar o seu cluster utilizando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
