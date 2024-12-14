import { useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "/components/ui/card";
import { Input } from "/components/ui/input";
import { Button } from "/components/ui/button";
import { Label } from "/components/ui/label";

const Cantieri = () => {
  const [cantieri, setCantieri] = useState([]);
  const [nomeCantiere, setNomeCantiere] = useState("");
  const [id, setId] = useState(1);
  const [showMenu, setShowMenu] = useState(null);
  const [showPage, setShowPage] = useState(null);
  const [operai, setOperai] = useState([]);
  const [data, setData] = useState("");
  const [nomeOperaio, setNomeOperaio] = useState("");
  const [costo, setCosto] = useState("");
  const [note, setNote] = useState("");
  const [trasporti, setTrasporti] = useState([]);
  const [veicolo, setVeicolo] = useState("");
  const [autista, setAutista] = useState("");
  const [totalTrasportiCost, setTotalTrasportiCost] = useState(0);

  // New state variables for invoices
  const [fattureClienti, setFattureClienti] = useState([]);
  const [fattureRicevute, setFattureRicevute] = useState([]);
  const [numeroFattura, setNumeroFattura] = useState("");
  const [oggetto, setOggetto] = useState("");
  const [fornitore, setFornitore] = useState("");
  const [imponibile, setImponibile] = useState("");
  const [iva, setIva] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    const nuovoCantiere = {
      id: id,
      nome: nomeCantiere,
      dataInserimento: new Date().toLocaleString(),
      dataUltimaModifica: new Date().toLocaleString(),
    };
    setCantieri([...cantieri, nuovoCantiere]);
    setNomeCantiere("");
    setId(id + 1);
  };

  const handleEsplora = (id) => {
    setShowMenu(id);
  };

  const handleMenu = (menu) => {
    setShowPage(menu);
  };

  const handleBack = () => {
    setShowPage(null);
  };

  const handleSubmitOperaio = (e) => {
    e.preventDefault();
    const nuovoOperaio = {
      data: data,
      nome: nomeOperaio,
      costo: parseFloat(costo),
      note: note,
    };
    setOperai([...operai, nuovoOperaio]);
    setData("");
    setNomeOperaio("");
    setCosto("");
    setNote("");
  };
  const handleSubmitTrasporto = (e) => {
    e.preventDefault();
    const nuovoTrasporto = {
      veicolo: veicolo,
      costo: parseFloat(costo),
      data: data,
    };
    setTrasporti((prevTrasporti) => {
      const updatedTrasporti = [...prevTrasporti, nuovoTrasporto];
      const totalTrasportiCost = updatedTrasporti.reduce(
        (acc, trasporto) => acc + trasporto.costo,
        0
      );
      setTotalTrasportiCost(totalTrasportiCost.toFixed(2)); // Imposta il totale trasporti
      return updatedTrasporti;
    });
    setVeicolo("");
    setCosto("");
    setData("");
  };

  // Handle submitting invoice for "Fatture vs Clienti"
  const handleSubmitFatturaCliente = (e) => {
    e.preventDefault();
    const nuovaFattura = {
      data: data,
      numeroFattura: numeroFattura,
      oggetto: oggetto,
      fornitore: fornitore,
      imponibile: parseFloat(imponibile),
      iva: parseFloat(iva),
    };
    setFattureClienti([...fattureClienti, nuovaFattura]);
    setNumeroFattura("");
    setOggetto("");
    setFornitore("");
    setImponibile("");
    setIva("");
    setData("");
  };

  // Handle submitting invoice for "Fatture Ricevute"
  const handleSubmitFatturaRicevuta = (e) => {
    e.preventDefault();
    const nuovaFattura = {
      data: data,
      numeroFattura: numeroFattura,
      oggetto: oggetto,
      fornitore: fornitore,
      imponibile: parseFloat(imponibile),
      iva: parseFloat(iva),
    };
    setFattureRicevute([...fattureRicevute, nuovaFattura]);
    setNumeroFattura("");
    setOggetto("");
    setFornitore("");
    setImponibile("");
    setIva("");
    setData("");
  };

  const handleReport = () => {
    const cantiere = cantieri.find((cantiere) => cantiere.id === showMenu);
    const riepilogoCosti = trasporti
      .reduce((acc, trasporto) => acc + trasporto.costo, 0)
      .toFixed(2);
    const report = {
      nomeCantiere: cantiere.nome,
      dataAttuale: new Date().toLocaleString(),
      riepilogoCosti: riepilogoCosti,
    };
    const csv = Object.keys(report)
      .map((key) => `${key}: ${report[key]}`)
      .join("\n");
    const blob = new Blob([csv], { type: "text/csv" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = "report.csv";
    a.click();
  };

  const totalOperaiCost = operai.reduce(
    (total, operaio) => total + operaio.costo,
    0
  );

  // Calculate total invoice amounts
  const totalFattureClienti = fattureClienti.reduce(
    (total, fattura) => total + fattura.imponibile + fattura.iva,
    0
  );
  const totalFattureRicevute = fattureRicevute.reduce(
    (total, fattura) => total + fattura.imponibile + fattura.iva,
    0
  );

  return (
    <div className="max-w-3xl mx-auto p-4">
      <Card>
        <CardHeader>
          <CardTitle>Gestione Cantieri</CardTitle>
        </CardHeader>
        <CardContent>
          <form onSubmit={handleSubmit}>
            <div className="mb-4">
              <Label htmlFor="nomeCantiere">Nome Cantiere</Label>
              <Input
                id="nomeCantiere"
                value={nomeCantiere}
                onChange={(e) => setNomeCantiere(e.target.value)}
              />
            </div>
            <Button type="submit">Aggiungi Cantiere</Button>
          </form>
          <div className="mt-4">
            <h2 className="text-lg font-bold">Elenco Cantieri</h2>
            <ul>
              {cantieri.map((cantiere) => (
                <li key={cantiere.id} className="py-2 border-b border-gray-200">
                  <span className="font-bold">ID: {cantiere.id}</span>
                  <br />
                  <span>Nome: {cantiere.nome}</span>
                  <br />
                  <span>Data Inserimento: {cantiere.dataInserimento}</span>
                  <br />
                  <span>
                    Data Ultima Modifica: {cantiere.dataUltimaModifica}
                  </span>
                  <br />
                  <Button
                    variant="secondary"
                    onClick={() => handleEsplora(cantiere.id)}
                  >
                    Esplora
                  </Button>
                  {showMenu === cantiere.id && (
                    <div className="mt-2">
                      <Button
                        variant="secondary"
                        onClick={() => handleMenu("Operai")}
                      >
                        Operai
                      </Button>
                      <Button
                        variant="secondary"
                        onClick={() => handleMenu("Economia")}
                      >
                        Economia
                      </Button>
                      <Button
                        variant="secondary"
                        onClick={() => handleMenu("Video/Foto/Note")}
                      >
                        Video/Foto/Note
                      </Button>
                      <Button
                        variant="secondary"
                        onClick={() => handleMenu("Riepilogo")}
                      >
                        Riepilogo
                      </Button>
                      <Button
                        variant="secondary"
                        onClick={() => handleMenu("Trasporti")}
                      >
                        Trasporti
                      </Button>
                    </div>
                  )}
                </li>
              ))}
            </ul>
          </div>
        </CardContent>
      </Card>

      {showPage === "Operai" && (
        <div className="absolute top-0 left-0 w-full h-full bg-white p-4">
          <Card>
            <CardHeader>
              <CardTitle>Operai</CardTitle>
            </CardHeader>
            <CardContent>
              <Button variant="secondary" onClick={handleBack}>
                Torna indietro
              </Button>
              <Button variant="secondary" onClick={handleReport}>
                Report
              </Button>
              <form onSubmit={handleSubmitOperaio}>
                <div className="mb-4">
                  <Label htmlFor="data">Data</Label>
                  <Input
                    id="data"
                    type="date"
                    value={data}
                    onChange={(e) => setData(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="nomeOperaio">Nome Operaio</Label>
                  <Input
                    id="nomeOperaio"
                    value={nomeOperaio}
                    onChange={(e) => setNomeOperaio(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="costo">Costo</Label>
                  <Input
                    id="costo"
                    type="number"
                    value={costo}
                    onChange={(e) => setCosto(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="note">Note</Label>
                  <Input
                    id="note"
                    value={note}
                    onChange={(e) => setNote(e.target.value)}
                  />
                </div>
                <Button type="submit">Aggiungi Operaio</Button>
              </form>
              <div className="mt-4">
                <h2 className="text-lg font-bold">Elenco Operai</h2>
                <ul>
                  {operai.map((operaio, index) => (
                    <li key={index} className="py-2 border-b border-gray-200">
                      <span>Data: {operaio.data}</span>
                      <br />
                      <span>Nome: {operaio.nome}</span>
                      <br />
                      <span>Costo: {operaio.costo}</span>
                      <br />
                      <span>Note: {operaio.note}</span>
                    </li>
                  ))}
                </ul>
                <div className="mt-4 font-bold">
                  Totale Costi Operai: {totalOperaiCost} €
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
      )}

      {showPage === "Economia" && (
        <div className="absolute top-0 left-0 w-full h-full bg-white p-4">
          <Card>
            <CardHeader>
              <CardTitle>Economia</CardTitle>
            </CardHeader>
            <CardContent>
              <Button variant="secondary" onClick={handleBack}>
                Torna indietro
              </Button>
              <form onSubmit={handleSubmitFatturaCliente}>
                <h2 className="text-lg font-bold mt-4">Fatture vs Clienti</h2>
                <div className="mb-4">
                  <Label htmlFor="data">Data</Label>
                  <Input
                    id="data"
                    type="date"
                    value={data}
                    onChange={(e) => setData(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="numeroFattura">Numero Fattura</Label>
                  <Input
                    id="numeroFattura"
                    value={numeroFattura}
                    onChange={(e) => setNumeroFattura(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="oggetto">Oggetto</Label>
                  <Input
                    id="oggetto"
                    value={oggetto}
                    onChange={(e) => setOggetto(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="fornitore">Fornitore</Label>
                  <Input
                    id="fornitore"
                    value={fornitore}
                    onChange={(e) => setFornitore(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="imponibile">Imponibile</Label>
                  <Input
                    id="imponibile"
                    type="number"
                    value={imponibile}
                    onChange={(e) => setImponibile(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="iva">IVA</Label>
                  <Input
                    id="iva"
                    type="number"
                    value={iva}
                    onChange={(e) => setIva(e.target.value)}
                  />
                </div>
                <Button type="submit">Aggiungi Fattura vs Cliente</Button>
              </form>
              <div className="mt-4">
                <h2 className="text-lg font-bold">Elenco Fatture vs Clienti</h2>
                <ul>
                  {fattureClienti.map((fattura, index) => (
                    <li key={index} className="py-2 border-b border-gray-200">
                      <span>Data: {fattura.data}</span>
                      <br />
                      <span>Numero Fattura: {fattura.numeroFattura}</span>
                      <br />
                      <span>Oggetto: {fattura.oggetto}</span>
                      <br />
                      <span>Fornitore: {fattura.fornitore}</span>
                      <br />
                      <span>Imponibile: {fattura.imponibile}</span>
                      <br />
                      <span>IVA: {fattura.iva}</span>
                    </li>
                  ))}
                </ul>
                <div className="mt-4 font-bold">
                  Totale Fatture vs Clienti: {totalFattureClienti} €
                </div>
              </div>

              <form onSubmit={handleSubmitFatturaRicevuta}>
                <h2 className="text-lg font-bold mt-4">Fatture Ricevute</h2>
                <div className="mb-4">
                  <Label htmlFor="data">Data</Label>
                  <Input
                    id="data"
                    type="date"
                    value={data}
                    onChange={(e) => setData(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="numeroFattura">Numero Fattura</Label>
                  <Input
                    id="numeroFattura"
                    value={numeroFattura}
                    onChange={(e) => setNumeroFattura(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="oggetto">Oggetto</Label>
                  <Input
                    id="oggetto"
                    value={oggetto}
                    onChange={(e) => setOggetto(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="fornitore">Fornitore</Label>
                  <Input
                    id="fornitore"
                    value={fornitore}
                    onChange={(e) => setFornitore(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="imponibile">Imponibile</Label>
                  <Input
                    id="imponibile"
                    type="number"
                    value={imponibile}
                    onChange={(e) => setImponibile(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="iva">IVA</Label>
                  <Input
                    id="iva"
                    type="number"
                    value={iva}
                    onChange={(e) => setIva(e.target.value)}
                  />
                </div>
                <Button type="submit">Aggiungi Fattura Ricevuta</Button>
              </form>
              <div className="mt-4">
                <h2 className="text-lg font-bold">Elenco Fatture Ricevute</h2>
                <ul>
                  {fattureRicevute.map((fattura, index) => (
                    <li key={index} className="py-2 border-b border-gray-200">
                      <span>Data: {fattura.data}</span>
                      <br />
                      <span>Numero Fattura: {fattura.numeroFattura}</span>
                      <br />
                      <span>Oggetto: {fattura.oggetto}</span>
                      <br />
                      <span>Fornitore: {fattura.fornitore}</span>
                      <br />
                      <span>Imponibile: {fattura.imponibile}</span>
                      <br />
                      <span>IVA: {fattura.iva}</span>
                    </li>
                  ))}
                </ul>
                <div className="mt-4 font-bold">
                  Totale Fatture Ricevute: {totalFattureRicevute} €
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
      )}

      {showPage === "Trasporti" && (
        <div className="absolute top-0 left-0 w-full h-full bg-white p-4">
          <Card>
            <CardHeader>
              <CardTitle>Trasporti</CardTitle>
            </CardHeader>
            <CardContent>
              <Button variant="secondary" onClick={handleBack}>
                Torna indietro
              </Button>
              <form onSubmit={handleSubmitTrasporto}>
                <div className="mb-4">
                  <Label htmlFor="veicolo">Veicolo</Label>
                  <Input
                    id="veicolo"
                    value={veicolo}
                    onChange={(e) => setVeicolo(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="autista">Autista</Label>
                  <Input
                    id="autista"
                    value={autista}
                    onChange={(e) => setAutista(e.target.value)}
                  />
                </div>
                <div className="mb-4">
                  <Label htmlFor="costo">Costo</Label>
                  <Input
                    id="costo"
                    type="number"
                    value={costo}
                    onChange={(e) => setCosto(e.target.value)}
                  />
                </div>
                <Button type="submit">Aggiungi Trasporto</Button>
              </form>
              <div className="mt-4">
                <h2 className="text-lg font-bold">Elenco Trasporti</h2>
                <ul>
                  {trasporti.map((trasporto, index) => (
                    <li key={index} className="py-2 border-b border-gray-200">
                      <span>Veicolo: {trasporto.veicolo}</span>
                      <br />
                      <span>Autista: {trasporto.autista}</span>
                      <br />
                      <span>Costo: {trasporto.costo}</span>
                    </li>
                  ))}
                </ul>
                <div className="mt-4 font-bold">
                  Totale Trasporti: {totalTrasportiCost} €
                </div>
              </div>
            </CardContent>
          </Card>
        </div>
      )}
    </div>
  );
};

export default Cantieri;
