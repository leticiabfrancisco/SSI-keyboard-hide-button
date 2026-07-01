# Self Service Issuing — Screen Templates

## Splash Screen (Home)

The entry point of the app. No PageHead, no Footer, no Stepper.

```tsx
export const SplashScreen = () => {
  const [selectedLanguage, setSelectedLanguage] = useState<string | null>(null);

  return (
    <Box sx={{
      height: '100vh',
      bgcolor: 'surface.surface',
      display: 'flex',
      flexDirection: 'column',
      alignItems: 'center',
      justifyContent: 'space-between',
      px: 1,
      py: 6,
    }}>
      {/* Top section: merchant logo + heading */}
      <Box sx={{ flex: 1, display: 'flex', flexDirection: 'column', alignItems: 'center', justifyContent: 'center' }}>
        <Box sx={{ mb: 4 }}>
          <img src={merchantLogo} alt="Merchant" style={{ height: 40 }} />
        </Box>

        <Typography variant="h1" align="center" sx={{ mb: 7 }}>
          Get your Tax Free refund with Planet
        </Typography>

        {/* Language selection card */}
        <Paper elevation={5} sx={{ borderRadius: 3, p: 4, width: '100%', maxWidth: 800 }}>
          <Stack direction="row" alignItems="center" spacing={1} sx={{ mb: 3, justifyContent: 'center' }}>
            <LanguageIcon />
            <Typography variant="h5">Select your language</Typography>
          </Stack>

          <Box sx={{ display: 'grid', gridTemplateColumns: 'repeat(5, 1fr)', gap: 2 }}>
            {languages.map(lang => (
              <Button
                key={lang.code}
                variant="outlined"
                size="large"
                onClick={() => setSelectedLanguage(lang.code)}
                sx={{ borderRadius: 2, height: 56, borderColor: 'outline.outlinelow' }}
              >
                {lang.name}
              </Button>
            ))}
          </Box>
        </Paper>
      </Box>

      {/* Soft consent */}
      <Typography variant="body2" color="text.secondary" align="center" sx={{ mt: 4 }}>
        By continuing, I agree with the <Link href="/terms">Terms and conditions</Link> and <Link href="/privacy">Privacy policy</Link>.
      </Typography>

      {/* Planet logo */}
      <Box sx={{ mt: 2 }}>
        <PlanetLogoSmall />
      </Box>
    </Box>
  );
};
```

---

## Wizard Step Layout (Shared Shell)

Every step (1–4) uses this wrapper layout:

```tsx
interface WizardStepProps {
  activeStep: number;        // 0-indexed (0=Receipts, 1=Profile, 2=Refund, 3=Form)
  children: ReactNode;       // Step-specific content
  showBack?: boolean;        // Show Back button (default: activeStep > 0)
  onCancel: () => void;
  onBack?: () => void;
  onNext: () => void;
  nextLabel?: string;        // Default: "Next"
  nextDisabled?: boolean;
}

export const WizardStep = ({
  activeStep,
  children,
  showBack = activeStep > 0,
  onCancel,
  onBack,
  onNext,
  nextLabel = 'Next',
  nextDisabled = false,
}: WizardStepProps) => (
  <Box sx={{ height: '100vh', display: 'flex', flexDirection: 'column', bgcolor: 'surface.surface' }}>
    {/* PageHead */}
    <Box sx={{ height: 57, display: 'flex', alignItems: 'center', px: 2, gap: 1, borderBottom: 1, borderColor: 'divider', bgcolor: 'background.paper' }}>
      <PlanetLogoSmall sx={{ width: 24 }} />
      <Typography variant="h6">Tax Free form</Typography>
    </Box>

    {/* Content area */}
    <Box sx={{ flex: 1, overflow: 'auto', display: 'flex', flexDirection: 'column', alignItems: 'center', pt: 4 }}>
      {/* Stepper */}
      <Stepper activeStep={activeStep} sx={{ width: 384, mb: 6 }}>
        <Step><StepLabel>Receipts</StepLabel></Step>
        <Step><StepLabel>Profile</StepLabel></Step>
        <Step><StepLabel>Refund</StepLabel></Step>
        <Step><StepLabel>Form</StepLabel></Step>
      </Stepper>

      {/* Step content — max 800px wide */}
      <Box sx={{ width: '100%', maxWidth: 800 }}>
        {children}
      </Box>
    </Box>

    {/* Footer */}
    <Box sx={{
      height: 104,
      px: 3,
      display: 'flex',
      alignItems: 'center',
      justifyContent: 'space-between',
      borderTop: 1,
      borderColor: 'divider',
      bgcolor: 'background.paper',
    }}>
      <Button variant="outlined" size="large" onClick={onCancel}>Cancel</Button>
      <Stack direction="row" spacing={2} alignItems="center">
        {showBack && (
          <Button variant="text" size="large" startIcon={<ChevronLeftIcon />} onClick={onBack}>
            Back
          </Button>
        )}
        <Button variant="contained" size="large" onClick={onNext} disabled={nextDisabled}>
          {nextLabel}
        </Button>
      </Stack>
    </Box>
  </Box>
);
```

---

## Step 1: Receipts (Empty State)

```tsx
export const ReceiptsEmptyStep = () => (
  <WizardStep activeStep={0} onCancel={handleCancel} onNext={handleNext} nextDisabled>
    <Paper elevation={5} sx={{ borderRadius: 3, p: 4, display: 'flex', alignItems: 'center', justifyContent: 'space-between' }}>
      <Box>
        <Typography variant="h5" sx={{ mb: 2 }}>
          Scan the barcode of the receipt
        </Typography>
        <Button variant="contained" size="large" startIcon={<CameraIcon />} sx={{ bgcolor: 'common.black', color: 'common.white', '&:hover': { bgcolor: 'grey.900' } }}>
          Start scanning
        </Button>
      </Box>
      <Box sx={{ width: 160, height: 160 }}>
        <img src={receiptIllustration} alt="" />
      </Box>
    </Paper>
  </WizardStep>
);
```

---

## Step 1: Receipts (With Items)

```tsx
export const ReceiptsWithItemsStep = ({ receipts }: { receipts: Receipt[] }) => (
  <WizardStep activeStep={0} onCancel={handleCancel} onNext={handleNext}>
    <Stack spacing={3}>
      {receipts.map((receipt, index) => (
        <ReceiptCard
          key={receipt.id}
          receiptNumber={index + 1}
          items={receipt.items}
          expanded={receipt.expanded}
          onToggle={() => toggleReceipt(receipt.id)}
          onDelete={() => deleteReceipt(receipt.id)}
        />
      ))}

      {/* Alert about eligibility */}
      <Alert severity="info">
        Items marked as "Not eligible" cannot be included in your Tax Free refund.
      </Alert>

      {/* Add another receipt */}
      <Button variant="outlined" size="large" startIcon={<CameraIcon />} fullWidth>
        Scan another receipt
      </Button>
    </Stack>
  </WizardStep>
);
```

---

## Step 2: Personal Details

```tsx
export const PersonalDetailsStep = ({ personalData, onRescan }: Props) => (
  <WizardStep activeStep={1} onCancel={handleCancel} onBack={handleBack} onNext={handleNext}>
    <Stack spacing={3}>
      {/* Personal details card */}
      <PersonalDetailsCard {...personalData} onRescan={onRescan} />

      {/* Country of residence */}
      <Paper elevation={5} sx={{ borderRadius: 3, p: 3 }}>
        <Typography variant="h5" sx={{ mb: 2 }}>Choose your country of residence</Typography>
        <Autocomplete
          size="medium"
          options={countries}
          getOptionLabel={(option) => option.name}
          renderInput={(params) => <TextField {...params} label="Country of residence" required />}
          renderOption={(props, option) => (
            <li {...props}>
              <span style={{ marginRight: 8 }}>{option.flag}</span>
              {option.name}
            </li>
          )}
        />
      </Paper>

      {/* Email collection */}
      <Paper elevation={5} sx={{ borderRadius: 3, p: 3 }}>
        <Typography variant="h5">Add an email to get your form</Typography>
        <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
          No spam or marketing emails
        </Typography>
        <TextField size="medium" label="Email" required fullWidth type="email" />
      </Paper>
    </Stack>
  </WizardStep>
);
```

---

## Step 3: Refund Method

```tsx
export const RefundMethodStep = () => {
  const [method, setMethod] = useState<'card' | 'cash'>('card');

  return (
    <WizardStep activeStep={2} onCancel={handleCancel} onBack={handleBack} onNext={handleNext}>
      <Stack spacing={3}>
        {/* Method selection */}
        <Paper elevation={5} sx={{ borderRadius: 3, p: 3 }}>
          <Typography variant="h5" sx={{ mb: 2 }}>Select your refund method</Typography>
          <Stack direction="row" spacing={2}>
            <SelectableCard
              selected={method === 'card'}
              icon={<CreditCardIcon />}
              label="Card"
              onClick={() => setMethod('card')}
            />
            <SelectableCard
              selected={method === 'cash'}
              icon={<AccountBalanceWalletIcon />}
              label="Cash"
              onClick={() => setMethod('cash')}
            />
          </Stack>
        </Paper>

        {/* Contextual instruction */}
        {method === 'card' && (
          <Paper elevation={5} sx={{ borderRadius: 3, p: 4, display: 'flex', alignItems: 'center', justifyContent: 'space-between' }}>
            <Box>
              <Typography variant="h5" sx={{ mb: 1 }}>
                Tap, swipe or insert your card on the card reader
              </Typography>
              <Typography variant="body2" color="text.secondary">
                Your VAT refund goes to this card once approved
              </Typography>
            </Box>
            <Box sx={{ width: 140, height: 140, flexShrink: 0 }}>
              <img src={cardReaderIllustration} alt="" />
            </Box>
          </Paper>
        )}
      </Stack>
    </WizardStep>
  );
};
```

---

## Camera Scan Overlay

```tsx
export const CameraScanOverlay = ({ instruction, onCancel, viewportWidth = 928, viewportHeight = 598 }: CameraOverlayProps) => (
  <Box sx={{
    position: 'fixed',
    inset: 0,
    zIndex: 1300,
    display: 'flex',
    flexDirection: 'column',
    alignItems: 'center',
    justifyContent: 'center',
  }}>
    {/* Camera feed (behind everything) */}
    <Box component="video" sx={{ position: 'absolute', inset: 0, width: '100%', height: '100%', objectFit: 'cover' }} />

    {/* Dark overlay with transparent viewport */}
    <Box sx={{
      position: 'absolute',
      inset: 0,
      boxShadow: `0 0 0 9999px rgba(0, 0, 0, 0.6)`,
      width: viewportWidth,
      height: viewportHeight,
      borderRadius: 3,
      mx: 'auto',
      my: 'auto',
      top: 0,
      bottom: 0,
      left: 0,
      right: 0,
    }} />

    {/* Instruction text */}
    <Typography variant="h6" sx={{ color: 'common.white', position: 'relative', zIndex: 1, mb: 2, textAlign: 'center', maxWidth: 744 }}>
      {instruction}
    </Typography>

    {/* Viewport frame (visual border) */}
    <Box sx={{
      width: viewportWidth,
      height: viewportHeight,
      borderRadius: 3,
      border: '2px solid rgba(255,255,255,0.3)',
      position: 'relative',
      zIndex: 1,
    }} />

    {/* Cancel link */}
    <Button variant="text" onClick={onCancel} sx={{ color: 'common.white', mt: 4, position: 'relative', zIndex: 1 }}>
      Cancel
    </Button>
  </Box>
);
```

---

## Mobile: Receipts Screen

Use this layout for phone-sized flows where users review scanned receipts and continue to the next step.

Mobile UX rule: all interactive controls in this screen (buttons, chips, icon buttons, dropdowns, quantity selectors) must have a minimum touch target of `44x44`.

```tsx
interface MobileReceiptItem {
  id: string;
  name: string;
  quantity: number;
  amountEur: number;
  eligible: boolean;
}

interface MobileReceipt {
  id: string;
  title: string;
  date: string;
  storeName: string;
  items: MobileReceiptItem[];
  expanded?: boolean;
}

interface MobileReceiptsScreenProps {
  receipts: MobileReceipt[];
  activeFilter: 'all' | 'fashion' | 'food' | 'notEligible';
  onChangeFilter: (filter: 'all' | 'fashion' | 'food' | 'notEligible') => void;
  onToggleReceipt: (id: string) => void;
  onDeleteReceipt: (id: string) => void;
  onScanNew: () => void;
  onBack: () => void;
  onNext: () => void;
}

export const MobileReceiptsScreen = ({
  receipts,
  activeFilter,
  onChangeFilter,
  onToggleReceipt,
  onDeleteReceipt,
  onScanNew,
  onBack,
  onNext,
}: MobileReceiptsScreenProps) => {
  const totalRefund = receipts
    .flatMap((receipt) => receipt.items)
    .filter((item) => item.eligible)
    .reduce((sum, item) => sum + item.amountEur, 0);

  const hasReceipts = receipts.length > 0;

  return (
    <Box sx={{ minHeight: '100dvh', bgcolor: 'surface.surface', display: 'flex', flexDirection: 'column' }}>
      {/* Mobile top bar */}
      <Box
        sx={{
          height: 56,
          px: 2,
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'space-between',
          borderBottom: 1,
          borderColor: 'divider',
          bgcolor: 'background.paper',
        }}
      >
        <Button variant="text" size="large" onClick={onBack} sx={{ minHeight: 44 }}>
          Back
        </Button>
        <Typography variant="h6">Receipts</Typography>
        <Box sx={{ width: 56 }} />
      </Box>

      {/* Scrollable content */}
      <Box sx={{ flex: 1, overflow: 'auto', px: 2, pt: 2, pb: 14 }}>
        {!hasReceipts ? (
          <Paper elevation={5} sx={{ borderRadius: 3, p: 3, textAlign: 'center' }}>
            <Typography variant="h5" sx={{ mb: 1 }}>
              Scan your first receipt
            </Typography>
            <Typography variant="body2" color="text.secondary" sx={{ mb: 3 }}>
              Add receipts to estimate your refund before you continue.
            </Typography>
            <Button variant="contained" size="large" fullWidth onClick={onScanNew} sx={{ minHeight: 44 }}>
              Start scanning
            </Button>
          </Paper>
        ) : (
          <Stack spacing={2}>
            <Alert severity="warning">
              Remove any items you will use, open or leave in the EU.
            </Alert>

            <Stack direction="row" spacing={1} sx={{ overflowX: 'auto', pb: 0.5 }}>
              <Chip
                label="All"
                variant={activeFilter === 'all' ? 'filled' : 'outlined'}
                onClick={() => onChangeFilter('all')}
                sx={{ minHeight: 44 }}
              />
              <Chip
                label="Fashion"
                variant={activeFilter === 'fashion' ? 'filled' : 'outlined'}
                onClick={() => onChangeFilter('fashion')}
                sx={{ minHeight: 44 }}
              />
              <Chip
                label="Food"
                variant={activeFilter === 'food' ? 'filled' : 'outlined'}
                onClick={() => onChangeFilter('food')}
                sx={{ minHeight: 44 }}
              />
              <Chip
                label="Not eligible"
                variant={activeFilter === 'notEligible' ? 'filled' : 'outlined'}
                onClick={() => onChangeFilter('notEligible')}
                sx={{ minHeight: 44 }}
              />
            </Stack>

            {receipts.map((receipt) => (
              <Paper key={receipt.id} elevation={5} sx={{ borderRadius: 3, overflow: 'hidden' }}>
                <Box
                  sx={{
                    px: 2,
                    py: 1.5,
                    display: 'flex',
                    alignItems: 'center',
                    justifyContent: 'space-between',
                    borderBottom: receipt.expanded ? 1 : 0,
                    borderColor: 'divider',
                  }}
                >
                  <Box>
                    <Typography variant="subtitle2">{receipt.title}</Typography>
                    <Typography variant="caption" color="text.secondary">
                      {receipt.storeName} • {receipt.date}
                    </Typography>
                  </Box>
                  <Stack direction="row" spacing={0.5}>
                    <IconButton
                      size="medium"
                      aria-label="toggle receipt details"
                      onClick={() => onToggleReceipt(receipt.id)}
                      sx={{ width: 44, height: 44 }}
                    >
                      <ExpandMoreIcon
                        sx={{
                          transform: receipt.expanded ? 'rotate(180deg)' : 'rotate(0deg)',
                          transition: 'transform 150ms ease',
                        }}
                      />
                    </IconButton>
                    <IconButton
                      size="medium"
                      aria-label="delete receipt"
                      onClick={() => onDeleteReceipt(receipt.id)}
                      sx={{ width: 44, height: 44 }}
                    >
                      <DeleteOutlineIcon />
                    </IconButton>
                  </Stack>
                </Box>

                {receipt.expanded && (
                  <Stack sx={{ px: 2, py: 1 }}>
                    {receipt.items.map((item) => (
                      <Box
                        key={item.id}
                        sx={{
                          minHeight: 64,
                          display: 'grid',
                          gridTemplateColumns: '1fr auto',
                          alignItems: 'center',
                          py: 1,
                          borderBottom: 1,
                          borderColor: 'divider',
                          '&:last-of-type': { borderBottom: 0 },
                        }}
                      >
                        <Box>
                          <Typography variant="subtitle2">{item.name}</Typography>
                          <Typography variant="caption" color="text.secondary">
                            Qty {item.quantity} {item.eligible ? '' : '• Not eligible'}
                          </Typography>
                        </Box>
                        <Typography variant="subtitle2">{item.amountEur.toFixed(2)} EUR</Typography>
                      </Box>
                    ))}
                  </Stack>
                )}
              </Paper>
            ))}

            <Button variant="outlined" size="large" fullWidth onClick={onScanNew} sx={{ minHeight: 44 }}>
              Scan new receipt
            </Button>
          </Stack>
        )}
      </Box>

      {/* Sticky mobile footer */}
      <Box
        sx={{
          position: 'fixed',
          bottom: 0,
          left: 0,
          right: 0,
          p: 2,
          borderTop: 1,
          borderColor: 'divider',
          bgcolor: 'background.paper',
        }}
      >
        <Stack spacing={1.25}>
          <Box sx={{ display: 'flex', alignItems: 'center', justifyContent: 'space-between' }}>
            <Typography variant="body2" color="text.secondary">
              Total estimated refund
            </Typography>
            <Typography variant="subtitle2">{totalRefund.toFixed(2)} EUR</Typography>
          </Box>
          <Button variant="contained" size="large" fullWidth onClick={onNext} disabled={!hasReceipts} sx={{ minHeight: 44 }}>
            Continue
          </Button>
        </Stack>
      </Box>
    </Box>
  );
};
```
