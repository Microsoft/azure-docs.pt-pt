---
title: Falha na startup de exemplo de papel nos Serviços Azure Cloud (suporte alargado)
description: Falha de arranque de exemplo de resolução de problemas em Azure Cloud Services (suporte alargado).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748966"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Resolução de problemas Azure Cloud Services (suporte alargado) que não começam

Eis alguns problemas e soluções comuns relacionados com os papéis dos Azure Cloud Services (suporte alargado) que não começam.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>A operação de serviço em nuvem falha com RoleInstanceStartupTimeoutError

Uma ou mais das suas funções nos Serviços Azure Cloud (suporte alargado) podem ser lentas a iniciar, ou podem estar a reciclar e a instância de função falha. O erro de aplicação `RoleInstanceStartupTimeoutError` da função aparece.

A aplicação de funções contém duas partes que podem causar a reciclagem de funções: *Tarefas de arranque* e *código de função (Implementação do RoleEntryPoint)*. 

Se a função parar, a plataforma como agente de serviço (PaaS) reinicia o papel.

Pode obter o estado atual e detalhes para uma instância de função para diagnosticar erros usando o PowerShell ou o portal Azure:

* **PowerShell**: Utilize o [cmdlet Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) para obter informações sobre o estado de funcionamento da instância de função:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Portal Azure**: No portal, vá para a instância de serviço na nuvem. Para ver os detalhes do estado, selecione **Roles and Instances** e, em seguida, selecione a instância de função.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Screenshot que mostra uma falha de startup de papel no portal Azure.":::

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências

Papéis e funções sem resposta que ciclo entre estados podem ser causados por DLLs ou conjuntos desaparecidos.

Aqui estão alguns sintomas de DLLs ou conjuntos desaparecidos:

* O seu papel de exemplo passa pelos estados **Inicializando,** **Ocupado** e **Parando**.
* A sua instância de função mudou-se para o estado **Ready,** mas se for à sua aplicação web, a página não é visível.


Um website que seja implantado numa função web e que falte a um DLL pode apresentar este erro de tempo de execução do servidor:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Screenshot que mostra um erro de tempo de execução após uma falha de arranque de função.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Resolver DLLs e conjuntos desaparecidos

Para resolver erros de DLLs e conjuntos em falta:

1. No Visual Studio, abra a solução.
2. No Solution Explorer, abra a pasta *Referências.*
3. Selecione o conjunto que está identificado no erro.
4. Em **Propriedades,** desateia a propriedade **Copy Local** à **True**.
5. Reimplantar o serviço de nuvem.

Depois de verificar se os erros já não aparecem, reimplante o serviço. Quando configurar a implementação, não selecione a caixa de verificação **de funções Ativa IntelliTrace para .NET 4.**

## <a name="diagnose-role-instance-errors"></a>Diagnosticar erros de instância de função

Escolha entre as seguintes opções para diagnosticar problemas com instâncias de função.

### <a name="turn-off-custom-errors"></a>Desligue erros personalizados

Para visualizar informações completas de erro, no *ficheiroweb.config* para a função web, desacorra o modo de erro personalizado para `off` , e, em seguida, reimplante o serviço:

1. No Visual Studio, abra a solução.
2. No Solution Explorer, abra o ficheiro *web.config.*
3. Na `system.web` secção, adicione o seguinte código:

   ```xml
   <customErrors mode="Off" />
   ```

4. Guarde o ficheiro.
5. Reembalar e recolocar o serviço.

Quando o serviço é reenviado, uma mensagem de erro inclui o nome de conjuntos em falta ou DLLs.

### <a name="use-remote-desktop"></a>Utilizar o Ambiente de Trabalho Remoto

Utilize o Ambiente de Trabalho Remoto para aceder à função e visualizar informações completas de erro:

1. [Adicione a extensão de ambiente de trabalho remoto para serviços de nuvem Azure (suporte alargado)](enable-rdp.md).
2. No portal Azure, quando a instância de serviço na nuvem mostrar um estado **de Pronto,** utilize o Remote Desktop para iniciar sposição no serviço de cloud. Para obter mais informações, consulte [Connect to role instances utilizando o Remote Desktop](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Inscreva-se na máquina virtual utilizando as credenciais que usou para configurar o Ambiente de Trabalho Remoto.
4. Abra uma janela de Linha de Comandos.
5. Na introdução do comando, insira **ipconfig**. Note o valor devolvido para o endereço IPv4.
6. Abra o Microsoft Internet Explorer.
7. Na barra de endereços, insira o endereço IPv4 seguido de um corte e o nome do ficheiro predefinido da aplicação web. Por exemplo, `http://<IPv4 address>/default.aspx`.

Se for agora ao site, verá mensagens de erro que contêm mais informações. Eis um exemplo:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Screenshot que mostra um exemplo de uma mensagem de erro.":::
  
### <a name="use-the-compute-emulator"></a>Use o emulador computatório

Pode utilizar o emulador de computação Azure para diagnosticar e resolver problemas de dependências em falta e *erros deweb.config.* Quando utilizar este método para diagnosticar problemas, para obter melhores resultados, utilize um computador ou máquina virtual que tenha uma instalação limpa do Windows.

Para diagnosticar problemas utilizando o emulador computacional Azure:

1. Instale o [Azure SDK](https://azure.microsoft.com/downloads/).
2. No computador de desenvolvimento, construa o projeto de serviço em nuvem.
3. No File Explorer, no projeto de serviço na nuvem, vá para a pasta *bin\debug.*
4. Copie a pasta *.csx* e o ficheiro *.cscfg* para o computador que está a usar para depurar problemas.
5. Na máquina limpa, abra uma janela de aviso de comando Azure SDK.
6. Na hora de comando, **introduzacsrun.exe /devstore:start**.
7. Em seguida, **insira o csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**.
8. Quando a função começa, o Internet Explorer apresenta informações detalhadas sobre erros.

Se for necessário um diagnóstico mais elevado, pode utilizar ferramentas padrão de resolução de problemas do Windows.

### <a name="use-intellitrace"></a>Use IntelliTrace

Para funções de trabalhador e web que utilizem .NET Framework 4, pode utilizar [IntelliTrace](/visualstudio/debugger/intellitrace). IntelliTrace está disponível na Visual Studio Enterprise.

Para implementar o seu serviço em nuvem com o IntelliTrace ligado:

1. Confirme se o Azure SDK 1.3 ou posteriormente está instalado.
2. No Visual Studio, implemente a solução. Quando configurar a implementação, selecione a caixa de verificação **De Enable IntelliTrace para .NET 4.**
3. Após o início da instância de função, abra o Server Explorer.
4. Expandir o nó **Azure\Cloud Services.**
5. Expandir a implementação para listar instâncias de funções. Clique com a direita numa instância de papel.
6. Selecione **Ver registos IntelliTrace**.
7. **IntelliTrace Summary**, aceda aos **Dados de Exceção**.
8. Expandir **dados de exceção** e procurar um `System.IO.FileNotFoundException` erro:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Screenshot de dados de exceção para uma falha de arranque de função." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas de funções de serviço em nuvem utilizando dados de diagnóstico de computador Azure PaaS](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
