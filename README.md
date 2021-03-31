## Argentina CUIL/CUIT JS validation.
Inspired by 
* [Wikipedia](https://es.wikipedia.org/wiki/Clave_%C3%9Anica_de_Identificaci%C3%B3n_Tributaria#Procedimiento_para_obtener_el_d%C3%ADgito_verificador)
* https://github.com/juan88/cuit/blob/master/src/Cuit.php
* https://gist.github.com/mausch/2885737

Valid examples: 
1. 30-71492781-3
2. 30-70843653-0
3. 20-33817701-2
4. 20-33817701-3
```javascript
function processDni(value) {
    function format(n) {
        return n.replaceAll(/\D/g, '');
    }

    function validateCheckDigit(n) {
        let multipliers = [5, 4, 3, 2, 7, 6, 5, 4, 3, 2];
        let number = n.slice(0, -1);
        let type = n.slice(0, 2);

        let sum = 0;
        for (let i = 0; i < 10; i++) {
            sum += multipliers[i] * number[i];
        }

        let checkDigit = 11 - sum % 11;
        if (checkDigit == 11) {
            checkDigit = 0;
        } else if (checkDigit == 10) {
            /**
             * I am not sure about this section as it has never been tested :)
             * Some algorithms just consider such CUIL/CUIT as invalid
             * */
            let dni = extractRawDni(n);
            switch (type) {
                case 20:
                    return validateCheckDigit('23' + dni + '9')
                case 27:
                    return validateCheckDigit('23' + dni + '4')
                case 24:
                    return validateCheckDigit('23' + dni + '3')
                case 30:
                    return validateCheckDigit('33' + dni + '9')
                case 34:
                    return validateCheckDigit('33' + dni + '3')
                default:
                    return false
            }
        }
        return checkDigit == n.slice(-1)
    }

    function extractRawDni(val) {
        return String(val).slice(2, -1);
    }

    value = format(value);
    
    return {
        dni: extractRawDni(value),
        valid: /^(20|23|24|27|30|33|34)\d{9}$/.test(value) && validateCheckDigit(value)
    }
}
```
P.S. The code can be improved and simplified for sure. Sorry guys, I am not a JS developer :shipit:
