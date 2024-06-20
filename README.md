# Trabalhando-com-ASP.NET-Minimals-APIs

Para criar um projeto utilizando ASP.NET Minimals APIs com o objetivo de desenvolver uma API para registro de veículos com autenticação JWT para administradores, além da integração com Swagger e testes, podemos dividir o projeto em alguns módulos principais. Vamos detalhar cada parte e fornecer exemplos de código onde aplicável.

### Módulos do Projeto

1. **Configuração Inicial**
2. **Definição da API de Veículos**
3. **Autenticação JWT para Administradores**
4. **Integração com Swagger**
5. **Testes da API**

### 1. Configuração Inicial

Primeiramente, crie um novo projeto ASP.NET Minimal API no Visual Studio ou utilizando o .NET CLI:

```bash
dotnet new web -n RegistroVeiculosAPI
```

### 2. Definição da API de Veículos

Crie um endpoint para lidar com operações relacionadas a veículos. Vamos criar um controlador mínimo para demonstração:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

// Endpoint de exemplo para registro de veículo
app.MapPost("/api/veiculos", async (ILogger logger, Veiculo novoVeiculo) =>
{
    logger.LogInformation($"Registrando novo veículo: {novoVeiculo.Marca} {novoVeiculo.Modelo}");

    // Lógica para salvar o veículo no banco de dados

    return Results.Created("/api/veiculos", novoVeiculo);
});

app.Run();
```

### 3. Autenticação JWT para Administradores

Implemente a autenticação JWT para administradores usando pacotes como `Microsoft.AspNetCore.Authentication.JwtBearer`. Aqui está um exemplo simplificado de como configurar a autenticação:

```csharp
// Adicione ao método ConfigureServices no Program.cs
var key = Encoding.ASCII.GetBytes("chave_secreta_para_assinatura_jwt");

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateIssuer = false,
            ValidateAudience = false
        };
    });

// Adicione ao método Configure no Program.cs
app.UseAuthentication();
app.UseAuthorization();
```

### 4. Integração com Swagger

Integre o Swagger para documentar e testar a API. Adicione o pacote NuGet `Swashbuckle.AspNetCore` e configure-o:

```csharp
// Adicione ao método ConfigureServices no Program.cs
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Adicione ao método Configure no Program.cs
app.UseSwagger();
app.UseSwaggerUI(c =>
{
    c.SwaggerEndpoint("/swagger/v1/swagger.json", "Registro de Veículos API V1");
});
```

### 5. Testes da API

Utilize testes unitários para garantir a robustez da API. Aqui está um exemplo básico de teste usando xUnit:

```csharp
public class VeiculosControllerTests
{
    [Fact]
    public async Task RegistrarVeiculo_ReturnsCreatedResponse()
    {
        // Configuração do ambiente de teste (injeção de dependências, configurações de contexto, etc.)

        var controller = new VeiculosController(/* injetar dependências necessárias */);
        var novoVeiculo = new Veiculo { Marca = "Ford", Modelo = "Fiesta" };

        var result = await controller.RegistrarVeiculo(novoVeiculo);

        var createdResult = Assert.IsType<CreatedAtActionResult>(result);
        var returnValue = Assert.IsType<Veiculo>(createdResult.Value);
        Assert.Equal("Ford", returnValue.Marca);
        Assert.Equal("Fiesta", returnValue.Modelo);
    }
}
```

### Considerações Finais

Certifique-se de ajustar e expandir os exemplos conforme necessário para atender aos requisitos específicos do seu projeto. O uso de padrões de desenvolvimento como injeção de dependência, separação de responsabilidades e boas práticas de segurança são fundamentais para criar uma API robusta e segura.
