# Traduções e i18n
(Internacionalização - Nestjs-i18n)

## Tabela de Conteúdos <!-- omit in toc -->

- [Como Adicionar Mensagens](#como-adicionar-novas-traduções)
- [Como Configurar Idiomas no Frontend](#como-usar-arquivos-traduzidos-para-o-front)
- [Como consumir o pacote de idioma internamente em Regra de Negócio](#como-verificar-um-i18n-direto-num-serviço)

---

O dev-starter já vem pronto para garantir respostas API Poliglotas caso seu Front-end exija ou mude de língua no Painel do Usuário! Assim, o Backend não reenvia os retornos hardcoded (chumbados e sempre iguais), e sim lê um dicionário local a depender da chave.

## Como adicionar novas traduções

1. Vá à raiz em `src/i18n`.
2. O Boilerplate sempre checa os JSONs em pastas atreladas. Existe o `en` atualmente. 
3. Você cria a pasta `pt-BR` e espelha os arquivos copiando e modificando (exemplo, copie os valores do JSON dict de validações e converta as regras "Missing Field" do class-validator para português!)

## Como usar arquivos traduzidos para o Front
1. Adicione/configure o Fetch das APIs com o novo cabeçalho (`Header`) contendo a requisição:
   **`x-custom-lang`**: `pt-BR`
2. Ao receber esta flag de custom-lang no contexto do request (ou via Cookie local), o `i18n-module` se vira e transforma todas as Exceptions globais!

## Como verificar um I18N direto num serviço

Às vezes, nós precisamos chamar as strings na mão no meio de um arquivo Controller ou Mail. Como instanciar da nuvem? Muito simples.


```typescript
import { I18nContext } from 'nestjs-i18n';

// ...
@Injectable()
export class AlgotithmsGamificationService {
  // code ...

  async analyzeAndCheckTitleName(): Promise<void> {

    // Invoca Estaticamente
    const i18n = I18nContext.current(); 

    if (!i18n) {
      throw new Error('I18nContext is not available em ambientes descontextualizados');
    }

    // Passa a DotKey que encontra o arquivo! Isso procura em "common.json" -> pela key "confirmEmail_Title_Name"
    const parsedLanguage = await i18n.t('common.confirmEmail');

    // ... e Usa a Váriavel "parsedLanguage" à vontade.
  }
}
```
