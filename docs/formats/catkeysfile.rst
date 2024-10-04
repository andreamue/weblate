import catkeys 
import csv 

FIELDNAMES_HEADER = ["version", "language", "signature", "checksum"]

class CatkeysHeader:
    """A catkeys translation memory header."""
    
    def __init__(self, header=None):
        self._header_dict = {}
        if not header:
            self._header_dict = self._create_default_header()
        elif isinstance(header, dict):
            for key in FIELDNAMES_HEADER:
                value = header.get(key, "")
                if value is None:
                    value = ""
                self._header_dict[key] = value

    @staticmethod
    def _create_default_header():
        """Create a default catkeys header."""
        return {
            "version": "1",                # Default version is "1"
            "language": "",                 # Default language is empty
            "signature": "",                # Default signature is empty
            "checksum": "",                 # Default checksum is empty
        }

    def parse_header(self, header_line):
        """Parse the header line of a catkeys file."""
        header_values = header_line.split("\t")  # Assuming tab-separated values
        if len(header_values) == 4:
            self._header_dict = {
                "version": header_values[0],
                "language": header_values[1].lower(),   # Ensure language is lowercase
                "signature": header_values[2],
                "checksum": header_values[3],
            }
        else:
            raise ValueError("Invalid CatKeys header format")
    
    def serialize_header(self):
        """Serialize the header back into a string for writing to the file."""
        return "\t".join([
            self._header_dict["version"],
            self._header_dict["language"],
            self._header_dict["signature"],
            self._header_dict["checksum"],
        ])
    
    def set_checksum(self, checksum):
        """Set the checksum for the file."""
        self._header_dict["checksum"] = str(checksum)
def parse(self, input):
    """Parse the given file or file source string."""
    if hasattr(input, "name"):
        self.filename = input.name
    elif not getattr(self, "filename", ""):
        self.filename = ""
    if hasattr(input, "read"):
        tmsrc = input.read()
        input.close()
        input = tmsrc
    input = input.decode(self.encoding)
    lines = input.split("\n")

    # Parse the first line as the header
    self.header.parse_header(lines[0])
    
    # Parse remaining lines as translation units
    reader = csv.DictReader(lines[1:], fieldnames=FIELDNAMES, dialect="catkeys")
    for line in reader:
        newunit = CatkeysUnit()
        newunit.dict = line
        self.addunit(newunit)
def serialize(self, out):
    output = csv.StringIO()
    writer = csv.DictWriter(output, FIELDNAMES, dialect="catkeys")

    # Calculate/update fingerprint (checksum)
    self.header.set_checksum(self._compute_fingerprint())

    # Write header
    output.write(self.header.serialize_header() + "\n")
    
    # Write translation units
    for unit in self.units:
        writer.writerow(unit.dict)
    
    out.write(output.getvalue().encode(self.encoding))

class CatkeysFile(base.TranslationStore):
    Name = "Haiku catkeys file"
    Mimetypes = ["application/x-catkeys"]
    Extensions = ["catkeys"]
    UnitClass = CatkeysUnit
