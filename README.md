# Žaidimas tic_tac_toe


"""
Kokia tai programa tai rrograma kuri leidzia vienam zmogui zaisti paprasta zaidima vartu tic tac toe su kompiuteriu.


kaip yra paleidziama programa?

1)paleidziama programa paprastai, tiesiog paspaudziant mygtuka run. Viskas kita bus vygdoma terminale.

2)Pirmas ejimas bus zaidejo ir po teisingo instrukciju ivedimo kita ejima padaryt kompiuteris.

3)Jei viskas bus padaryta pagal instrukcijas zaidimas pasibaiks vienu is 3 variantu: laimes zaidėjas, kompiuteris, arba zaidimas baigsis lygiosiomis.

Kaip žaidžiamas žaidimas
Taigi kaip minejau pirmas ėjimas visada bus jūsų. Terminale segdami instrukcijas turesite pasirinkt eilute ir stulpelį kur norite padeti savo X. jaigu bandysite padeti X kur jau yra zenklas jum tai bus pranesta.

Analizė:
"""

class Lenta:
    """
    reprezentuoja žaidimo lentą.
    """
    def __init__(self):
        """
        inicializuoja tuščią 3x3  lentą.
        """
        self._lenta = [[' ' for _ in range(3)] for _ in range(3)]

    def __str__(self):
        """
        Grąžina lenta teksto formatu.
        """
        return (
            "-------------\n"
            f"| {self._lenta[0][0]} | {self._lenta[0][1]} | {self._lenta[0][2]} |\n"
            "-------------\n"
            f"| {self._lenta[1][0]} | {self._lenta[1][1]} | {self._lenta[1][2]} |\n"
            "-------------\n"
            f"| {self._lenta[2][0]} | {self._lenta[2][1]} | {self._lenta[2][2]} |\n"
            "-------------"
        )

    def ar_laisvas(self, eile, stulpelis):
        """
        Patikrina, ar nurodytas lentos langelis yra laisvas. jeigu taip tada grazina true, o jeigu ne tada grazina false.

        """
        return self._lenta[eile][stulpelis] == ' '

    def padeti_zymekli(self, eile, stulpelis, zymeklis):
        """
        Padeda žymeklį į nustatyta lentos langelį, jeigu jisai nustatytas. Jeigu langelis jau uzimtas tada grazina false

        """
        if self.ar_laisvas(eile, stulpelis):
            self._lenta[eile][stulpelis] = zymeklis
            return True
        return False

    def ar_pilna(self):
        """
        Patikrina, ar visi lentos langeliai yra užimti. Jeigu taip tada grazina true jeigu ne tai grazina false.
            
        """
        for eile in self._lenta:
            if ' ' in eile:
                return False
        return True

    def _tikrinti_laimetoja(self, zymeklis):
        """
        Privatus metodas, kuris patikrina, ar yra trys vienodi zenklai stulpelije ar eilutehe ar istrizai. JEigu taip tai grazina true jeigu ne tada false.

        """
        # Tikrina eilutes
        for eile in self._lenta:
            if all(langelis == zymeklis for langelis in eile):
                return True
        # Tikrina stulpelius
        for stulpelis in range(3):
            if all(self._lenta[eile][stulpelis] == zymeklis for eile in range(3)):
                return True
        # Tikrina įstrižaines
        if all(self._lenta[i][i] == zymeklis for i in range(3)):
            return True
        if all(self._lenta[i][2 - i] == zymeklis for i in range(3)):
            return True
        return False

class Zaidėjas:
    """
    Bazė klasė žaidėjui.
    """
    def __init__(self, zymeklis):
        """
        Inicializuoja žaidėją su jam skirtu žymekliu. Arba X arba O.

        """
        self._zymeklis = zymeklis

    @property
    def zymeklis(self):
        """
        Parodo koks yra Žaidejo Žymeklis.
        """
        return self._zymeklis

    def gauti_ejima(self, lenta):
        """
        Abstraktus metodas, kuris skirtas gauti zaidejo ejima. Jis turi but perasytas konkrecioje zaideju klaseje.
        """
        raise NotImplementedError("perasyti reikes")

class Žmogus(Zaidėjas):
    """
    Klasė, kuri skirta žmogaus žaidejui.
    """
    def gauti_ejima(self, lenta):
        """
        Leidžia žmogui per koncole ivesti savo ejima.

        """
        while True:
            try:
                eile = int(input("įvesk eilės numerį (0-2): "))
                stulpelis = int(input("įvesk stulpelio numerį (0-2): "))
                if 0 <= eile <= 2 and 0 <= stulpelis <= 2 and lenta.ar_laisvas(eile, stulpelis):
                    return eile, stulpelis
                else:
                    print("Užimtas langelis")
            except ValueError:
                print("blogai ivedei reikia skaičius nuo 0 iki 2.")

class Kompiuteris(Zaidėjas):
    """
    Klasė, reprezentuojanti kompiuterio žaidėją.
    """
    import random

    def gauti_ejima(self, lenta):
        """
        Generuojamas kompiuterio random ejimas i jau tuscia langeli.

        """
        while True:
            eile = self.random.randint(0, 2)
            stulpelis = self.random.randint(0, 2)
            if lenta.ar_laisvas(eile, stulpelis):
                return eile, stulpelis

class Žaidimas:
    """
    Klasė, kuri valdo zaidimo eiga.
    """
    def __init__(self, zaidejas1, zaidejas2):
        """
        Pradeda žaidimą su dviem žaidėjais ir sukuria žaidimo lentą.

        """
        self._lenta = Lenta()
        self._zaidejas1 = zaidejas1
        self._zaidejas2 = zaidejas2
        self._dabartinis_zaidejas = self._zaidejas1  # Pradeda pirmasis žaidėjas

    def _perjungti_zaideja(self):
        """
        Pakeičia dabartini zaidėja.
        """
        if self._dabartinis_zaidejas == self._zaidejas1:
            self._dabartinis_zaidejas = self._zaidejas2
        else:
            self._dabartinis_zaidejas = self._zaidejas1

    def pradeti_zaidima(self):
        """
        Prasideda zaidimas
        """
        print("tu žaidi Tic Tac Toe!")
        print(self._lenta)

        while True:
            print(f"eik žaidejau: {self._dabartinis_zaidejas.zymeklis}")
            eile, stulpelis = self._dabartinis_zaidejas.gauti_ejima(self._lenta)

            if self._lenta.padeti_zymekli(eile, stulpelis, self._dabartinis_zaidejas.zymeklis):
                print(self._lenta)
                if self._lenta._tikrinti_laimetoja(self._dabartinis_zaidejas.zymeklis):
                    print(f"Žaidėjas {self._dabartinis_zaidejas.zymeklis} laimėjo!")
                    break
                elif self._lenta.ar_pilna():
                    print("nieks nelaimi")
                    break
                else:
                    self._perjungti_zaideja()
            else:
                print("Kažkas blogai, bandyk dar kartą.")

if __name__ == "__main__":
    """
    Pagrindinė programos dalis, kuri sukuria žaidėjus ir pradeda žaidimą.
    """
    zmogus = Žmogus(zymeklis='X')
    kompiuteris = Kompiuteris(zymeklis='O')

    zaidimas = Žaidimas(zmogus, kompiuteris)
    zaidimas.pradeti_zaidima()
"""
Rezultatai:
Programa teisingai valdo zaidimo eiga tarp kompiuterio ir zmogaus), tikrina, ar galimas ejimas (ar langelija jau nera zenklo), nustato kas laimejo ir, ar yra lygiosios.
Isvados:
Žaidimas veikia puikiai, nors ir yra gana paprastas ir galesu but ivesta protingesnė mastymo sistema kaip isvekt 100 procentiniu pralaimejimų.

Patobulinimai:
galetu būti atskiras langas, kuris leidzia per ji zaisti zaidiam, o ne per terminala. Ir ivesta sistema kuri leistu pasirinkt ar zaisti pries kompiuteri, ar kita zaidėja gal net internetu.

"""

    
