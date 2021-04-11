---
title: Falha na startup de exemplo de função para a Azure Cloud Services (suporte alargado)
description: Falha de arranque de exemplo de fase de resolução de problemas para a Azure Cloud Services (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382360"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Resolução de problemas Azure Cloud Service (suporte alargado) que não começam
Eis alguns problemas e soluções comuns relacionados com os papéis dos Serviços cloud (suporte alargado) que não começam.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>A Operação serviço de nuvem falhou com roleInstanceStartupTimeoutError
Uma ou mais instâncias de função do seu serviço poderiam (suporte alargado) não podem começar no tempo estipulado. Estas instâncias de função podem estar a demorar algum tempo a iniciar ou podem estar a reciclar e a instância de função pode falhar com um RoleInstanceStartupTimeoutError Este é um erro de aplicação de função. A aplicação de funções contém duas partes principais: 'Startup Tasks' e 'Role code (Implementação do RoleEntryPoint)', ambas as quais podem causar a reciclagem de funções. Se o papel for acidentado, o Agente PaaS irá sempre relançá-lo. 

Para obter o estado atual e detalhes das instâncias de papel em caso de erros, utilize:

* PowerShell: Utilize o [cmdlet Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) para obter informações sobre o estado de tempo de funcionamento de uma instância de função num serviço de nuvem.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Portal Azure: Vá ao seu serviço de cloud e selecione o separador Funções e Instâncias. Clique na instância de função para obter os seus detalhes de estado 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="A imagem mostra falha de arranque de função no portal.":::
   
Eis alguns problemas e soluções comuns relacionados com os papéis dos Azure Cloud Services (suporte alargado) que não começam ou circulam entre os estados de inicialização, ocupados e de paragem.

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências
Papéis e papéis sem resposta que estão a pedalar entre estados de inicialização, busy e stop podem ser causados pela falta de DLLs ou conjuntos.
Os sintomas de DLLs ou conjuntos em falta podem ser:

* O seu exemplo de papel é andar de bicicleta através de Estados **de Inicialização,** **Ocupado** e **Paragem.**
* A sua instância de função mudou-se para **Ready** mas se navegar para a sua aplicação web, a página não aparece.

Existem vários métodos recomendados para investigar estas questões.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas de DLL em falta numa função web
Quando navega para um website que é implantado numa função web, e o navegador apresenta um erro do servidor semelhante ao seguinte, pode indicar que falta um DLL.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Imagem mostra erro de tempo de execução na falha de arranque de função":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desligando erros personalizados
Informações de erro mais completas podem ser visualizadas configurando o web.config para a função web definir o modo de erro personalizado para Desligar e redistribuir o serviço.
Para ver erros mais completos sem utilizar o Ambiente de Trabalho Remoto:
1.  Abra a solução no Microsoft Visual Studio.
2.  No Solution Explorer, localize o ficheiro web.config e abra-o.
3.  No ficheiro web.config, localize a secção system.web e adicione a seguinte linha:
 ```xml
<customErrors mode="Off" />
```
4.  Guarde o ficheiro.
5.  Reembalar e recolocar o serviço.
Uma vez que o serviço seja reenviado, verá uma mensagem de erro com o nome do conjunto em falta ou DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas visualizando o erro remotamente
Pode utilizar o Remote Desktop para aceder à função e visualizar informações de erro mais completas remotamente. Utilize os seguintes passos para visualizar os erros utilizando o Ambiente de Trabalho Remoto:
1.  Ativar a extensão remota do ambiente de trabalho para o Cloud Service (suporte alargado). Para obter mais informações, consulte [aplicar a extensão de Ambiente de Trabalho Remoto aos Serviços de Cloud (suporte alargado) utilizando o portal Azure](enable-rdp.md)
2.  No portal Azure, uma vez que o caso mostra um estado de Ready, remotamente no caso. Para obter mais informações sobre a utilização do ambiente de trabalho remoto com serviços cloud (suporte alargado), consulte [Connect to role instances with Remote Desktop](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Inscreva-se na máquina virtual utilizando as credenciais especificadas durante a configuração do Ambiente de Trabalho Remoto.
4.  Abra uma janela de comando.
5.  Tipo IPconfig.
6.  Note o valor do endereço IPv4.
7.  Abra o Internet Explorer.
8.  Digite o endereço e o nome da aplicação web. Por exemplo, http:// <IPV4 Address> /predefinido.aspx.
Navegar no site irá agora devolver mensagens de erro mais explícitas:
* Erro do servidor na aplicação '/' .
* Descrição: Ocorreu uma exceção não manipulada durante a execução do pedido web atual. Por favor, reveja o rastreio da pilha para obter mais informações sobre o erro e de onde foi originado no código.
* Detalhes da exceção: System.IO.FIleNotFoundException: Não foi possível carregar ficheiro ou montagem 'Microsoft.WindowsAzure.StorageClient, Versão=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado.
Por exemplo:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Imagem mostra exceção na falha de arranque de função":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador computacional
Pode utilizar o Emulador Azure Compute para diagnosticar e resolver problemas de dependências em falta e erros de web.config.
Para obter os melhores resultados na utilização deste método de diagnóstico, deve utilizar um computador ou máquina virtual que tenha uma instalação limpa do Windows. 
1.  Instale o [Azure SDK](https://azure.microsoft.com/downloads/) 
2.  Na máquina de desenvolvimento, construa o projeto de serviço em nuvem.
3.  No Windows Explorer, navegue para a pasta bin\debug do projeto de serviço de nuvem.
4.  Copie a pasta .csx e o ficheiro .cscfg para o computador que está a usar para depurar os problemas.
5.  Na máquina limpa, abra uma janela de solicitação de comando Azure SDK e escreva csrun.exe /devstore:start.
6.  Na solicitação de comando, digite o csrun <caminho para a pasta .csx> <caminho para .cscfg file> /launchBrowser.
7.  Quando a função começar, verá informações detalhadas de erro no Internet Explorer. Também pode utilizar ferramentas padrão de resolução de problemas do Windows para diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando IntelliTrace
Para funções de trabalhadores e web que utilizem .NET Framework 4, pode utilizar [o IntelliTrace,](https://docs.microsoft.com/visualstudio/debugger/intellitrace)que está disponível na Microsoft Visual Studio Enterprise.
Siga estes passos para implementar o serviço com o IntelliTrace ativado:
1.  Confirme se o Azure SDK 1.3 ou posteriormente está instalado.
2.  Implemente a solução utilizando o Visual Studio. Durante a implementação, verifique a caixa de verificação enable IntelliTrace para .NET 4.
3.  Assim que a instância começar, abra o Explorador do Servidor.
4.  Expanda o nó Azure\Cloud Services e localize a implantação.
5.  Expanda a implementação até ver os exemplos de papéis. Clique com o direito numa das ocorrências.
6.  Escolha ver os registos IntelliTrace. O Resumo IntelliTrace será aberto.
7.  Localize a secção de exceções do resumo. Se houver exceções, a secção será rotulada de Dados de Exceção.
8.  Expandir os dados de exceção e procurar por erros system.iO.FileNotFoundException semelhantes aos seguintes:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Imagem mostra dados de exceção sobre falha de arranque de função" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Endereço desaparecido DLLs e conjuntos
Para resolver os erros de DLL e de montagem em falta, siga estes passos:
1.  Abra a solução no Visual Studio.
2.  No Solution Explorer, abra a pasta Referências.
3.  Clique no conjunto identificado no erro.
4.  No painel propriedades, localize a propriedade Copy Local e desaceia o valor para True.
5.  Reimplantar o serviço de nuvem.
Uma vez verificado que todos os erros foram corrigidos, pode implementar o serviço sem verificar a caixa de verificação de funções Enable IntelliTrace para .NET 4.

## <a name="next-steps"></a>Passos seguintes 
- Para aprender a resolver problemas de problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS, consulte a [série de blogs de Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
