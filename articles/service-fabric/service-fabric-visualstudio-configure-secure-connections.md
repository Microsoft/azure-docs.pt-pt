---
title: Configurar conexões seguras do Cluster Service Fabric do Azure
description: Saiba como usar o Visual Studio para configurar conexões seguras com suporte no Cluster Service Fabric do Azure.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464088"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurar conexões seguras para um Cluster Service Fabric do Visual Studio
Saiba como usar o Visual Studio para acessar com segurança um cluster de Service Fabric do Azure com políticas de controle de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de conexão de cluster
Há suporte para dois tipos de conexões no Cluster Service Fabric do Azure: conexões **não seguras** e conexões seguras **baseadas em certificado X509** . (Para Service Fabric clusters hospedados localmente, as autenticações do **Windows** e do **dSTS** também têm suporte.) Você precisa configurar o tipo de conexão de cluster quando o cluster está sendo criado. Depois de criado, o tipo de conexão não pode ser alterado.

As ferramentas de Service Fabric do Visual Studio dão suporte a todos os tipos de autenticação para se conectar a um cluster para publicação. Consulte [Configurando um cluster Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter instruções sobre como configurar um cluster de Service Fabric seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurar conexões de cluster em perfis de publicação
Se você publicar um projeto de Service Fabric do Visual Studio, use a caixa de diálogo **publicar Service Fabric aplicativo** para escolher um cluster de Service Fabric do Azure. Em **ponto de extremidade de conexão**, selecione um cluster existente em sua assinatura.

![A caixa de diálogo * * Publicar Service Fabric aplicativo * * é usada para configurar uma conexão Service Fabric.][publishdialog]

A caixa de diálogo **publicar Service Fabric aplicativo** valida automaticamente a conexão do cluster. Se solicitado, entre em sua conta do Azure. Se a validação for aprovada, significa que o sistema tem os certificados corretos instalados para se conectar ao cluster com segurança ou o cluster não é seguro. As falhas de validação podem ser causadas por problemas de rede ou por não ter seu sistema configurado corretamente para se conectar a um cluster seguro.

![A caixa de diálogo * * Publicar Service Fabric aplicativo * * valida uma conexão de Cluster Service Fabric existente e configurada corretamente.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para se conectar a um cluster seguro
1. Verifique se você pode acessar um dos certificados de cliente que o cluster de destino confia. O certificado geralmente é compartilhado como um arquivo de troca de informações pessoais (. pfx). Consulte [Configurando um cluster Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md) para saber como configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado confiável. Para fazer isso, clique duas vezes no arquivo. pfx ou use o script Import-PfxCertificate do PowerShell para importar os certificados. Instale o certificado para **CERT: \ LocalMachine\My**. OK para aceitar todas as configurações padrão ao importar o certificado.
3. Escolha o comando **publicar...** no menu de atalho do projeto para abrir a caixa de diálogo **publicar aplicativo Azure** e, em seguida, selecione o cluster de destino. A ferramenta resolve automaticamente a conexão e salva os parâmetros de conexão segura no perfil de publicação.
4. Opcional: você pode editar o perfil de publicação para especificar uma conexão de cluster segura.
   
   Como você está editando manualmente o arquivo XML do perfil de publicação para especificar as informações do certificado, lembre-se de anotar o nome do repositório de certificados, o local de armazenamento e a impressão digital do certificado. Você precisará fornecer esses valores para o nome do repositório e o local do repositório do certificado. Consulte [como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) para obter mais informações.
   
   Você pode usar os parâmetros *ClusterConnectionParameters* para especificar os parâmetros do PowerShell a serem usados ao se conectar ao cluster de Service Fabric. Os parâmetros válidos são aqueles aceitos pelo cmdlet Connect-ServiceFabricCluster. Consulte [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) para obter uma lista de parâmetros disponíveis.
   
   Se estiver publicando em um cluster remoto, você precisará especificar os parâmetros apropriados para esse cluster específico. Veja a seguir um exemplo de conexão a um cluster não seguro:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Veja um exemplo para se conectar a um cluster seguro baseado em certificado X509:
   
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
5. Edite quaisquer outras configurações necessárias, como parâmetros de atualização e local do arquivo de parâmetro do aplicativo, e, em seguida, publique seu aplicativo na caixa de diálogo **publicar Service Fabric aplicativo** no Visual Studio.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como acessar clusters Service Fabric, consulte [visualizando seu cluster usando Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
