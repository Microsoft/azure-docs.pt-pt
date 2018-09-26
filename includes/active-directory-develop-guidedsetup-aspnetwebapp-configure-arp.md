
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Configurar a aplicação Web ASP.NET com informações de registo da aplicação

Neste passo, irá configurar seu projeto para utilizar SSL e, em seguida, utilize o URL do SSL para configurar informações de registo do seu aplicativo. Depois disso, adicionar a aplicação "informações de registo à sua solução através de *Web. config*.

1.  No Solution Explorer, selecione o projeto e observe o `Properties` janela (se não vir uma janela de propriedades, pressione F4)
2.  Alteração `SSL Enabled` para `True`
3.  Copie o valor de `SSL URL` acima e colá-lo no `Redirect URL` campo na parte superior desta página, em seguida, clique em *atualização*:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Adicione o seguinte na `web.config` localizado na pasta do raiz, na secção de ficheiro `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
