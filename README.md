function calculateDeltaP(P, T, vn, Sol_DelP, l, d, N_holes) {
    const rho_N2 = 1.27;
    const rho = 0.0285 * P / (8.314 * T);
    const c = Math.sqrt(1.4 * 8.314 * T / 0.0285);
    const Vn = vn * rho_N2 / rho;
    const Vg_tot = Vn / 60;
    const F0 = 1.38;
    const V0 = Vg_tot / F0;
  
    const F1 = N_holes * Math.PI * 0.25 * (d * 0.001) ** 2;
    const V1 = Vg_tot / F1;
    const Re = (V1 * d * 0.001) / (1.7 * 0.00001);
        
    const f = F1 / F0;
    
    const index = l / d;
    let y_pred = 0;
    const L1 = [0, 0.2, 0.4, 0.6, 0.8, 1, 1.4, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    const L2 = [1.35, 1.22, 1.1, 0.84, 0.42, 0.24, 0.1, 0.02, 0, 0, 0, 0, 0, 0, 0, 0];
    
    function linearInterpolation(x, x1, y1, x2, y2) {
        // Linear interpolation formula
        return y1 + ((x - x1) * (y2 - y1)) / (x2 - x1);
      }
      
      
      


    for (let i = 0; i < L1.length; i++) {
      if (index === L1[i]) {
        y_pred = L2[i];
      } else {
        if (L1[i] < index && L1[i + 1] > index) {

          const interpolatedY = linearInterpolation(index, L1[i], L2[i], L1[i + 1], L2[i + 1]);
          console.log(interpolatedY);  
          
        } else {
          y_pred = 1;
        }
      }
    }
  
    const F = [0, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 0.95];
    const Ren = [25, 40, 60, 100, 200, 400, 1000, 2000, 4000, 10000, 20000, 100000, 200000, 1000000];
    const epsilon_Ren = [0.34, 0.36, 0.37, 0.4, 0.42, 0.46, 0.53, 0.59, 0.64, 0.74, 0.81, 0.94, 0.95, 0.98];
  
    const Zeta_phi = [
      [1.94, 1.38, 1.14, 0.89, 0.59, 0.64, 0.39, 0.3, 0.22, 0.15, 0.11, 0.04, 0.01, 0],
      [1.78, 1.36, 1.05, 0.85, 0.67, 0.57, 0.36, 0.26, 0.2, 0.13, 0.09, 0.03, 0.01, 0],
      [1.57, 1.16, 0.88, 0.75, 0.57, 0.43, 0.3, 0.22, 0.17, 0.1, 0.07, 0.02, 0.01, 0],
      [1.35, 0.99, 0.79, 0.57, 0.4, 0.28, 0.19, 0.14, 0.1, 0.06, 0.04, 0.02, 0.01, 0],
      [1.1, 0.75, 0.55, 0.34, 0.19, 0.12, 0.07, 0.05, 0.03, 0.02, 0.01, 0.01, 0],
      [0.85, 0.56, 0.3, 0.19, 0.1, 0.06, 0.03, 0.02, 0.01, 0, 0, 0, 0],
      [0.58, 0.37, 0.23, 0.11, 0.06, 0.03, 0.02, 0.01, 0, 0, 0, 0, 0, 0],
      [0.4, 0.24, 0.13, 0.06, 0.03, 0.02, 0.01, 0, 0, 0, 0, 0, 0, 0],
      [0.2, 0.13, 0.08, 0.03, 0.01, 0, 0, 0, 0, 0, 0, 0, 0, 0,],
      [0.03, 0.03, 0.02, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
    ];
  
    let a = 0;
    let b = 0;
    let a1 = 0;
    let b1 = 0;
    let a2 = 0;
    let b2 = 0;
    let X1 = 0;
    let X2 = 0;
    let Y1 = 0;
    let Y2 = 0;
  
    for (let i = 0; i < F.length - 1; i++) {
      if (f === F[i]) {
        
        a = i;
      } else {
        if (F[i] < f && F[i + 1] > f) {
          X1 = F[i];
          X2 = F[i + 1];
          a1 = i;
          b1 = i + 1;
        } else {
          X1 = 0.0001;
          X2 = 1;
        }
      }
    }
  
    let eps1 = 0;
    let eps2 = 0;
  
    for (let i = 0; i < Ren.length - 1; i++) {
      if (Re === Ren[i]) {
        
        b = i;
      } else {
        if (Ren[i] < Re && Ren[i + 1] > Re) {
          Y1 = Ren[i];
          Y2 = Ren[i + 1];
          a2 = i;
          b2 = i + 1;
          eps1 = epsilon_Ren[i];
          eps2 = epsilon_Ren[i + 1];
        } else {
          Y1 = 10;
          Y2 = 10000000;
        }
      }
    }
  
    const z11 = Zeta_phi[a1][a2];
    const z12 = Zeta_phi[a1][b2];
    const z21 = Zeta_phi[b1][a2];
    const z22 = Zeta_phi[b1][b2];
  
    const Z_phi = 1 / (((X2 - X1) * (Y2 - Y1)) * (z11 * (X2 - f) * (Y2 - Re) + z21 * (f - X1) * (Y2 - Re) + z12 * (X2 - f) * (Re - Y1) + z22 * (f - X1) * (Re - Y1)));
  
    const lambda = eps1 + (eps2 - eps1) / ((Y2 - Y1) * (index - Y1));
  
    const Zeta_sh_tu = ((1 + 0.707 * Math.sqrt(1 - f) - f) / f) ** 2;
    const Zeta_sh_tr = (Z_phi + lambda * (1 + 0.707 * Math.sqrt(1 - f) - f) ** 2) / f ** 2;
    const Zeta_th_tu = (0.5 * (1 - f) + y_pred * (1 - f) ** 1.5 + lambda * index) / f ** 2;
    const Zeta_th_tr = (Z_phi + lambda * (0.5 * (1 - f) + y_pred * (1 - f) ** 1.5 + (1 - f) ** 2) + lambda * index) / f ** 2;
  
    let Km = 0;
    const Ma = V1 / c;
    let Z = 0;
  
    if (Ma < 0.3) {
      Km = 1;
    } else {
      Km = 35.252 * Ma ** 4 - 28.883 * Ma ** 3 + 8.1252 * Ma ** 2 - 0.6808 * Ma + 1;
    }
  
    if (index < 0.015) {
      if (Re < 100000) {
        Z = Zeta_sh_tr;
      } else {
        Z = Zeta_sh_tu;
      }
    } else {
      if (Re < 100000) {
        Z = Zeta_th_tr;
      } else {
        Z = Zeta_th_tu;
      }
    }
  
    const Delta_P = 0.5 * Z * rho * V0 ** 2 / 100;
  
    const Ist_P = Delta_P - Sol_DelP;
    return Ist_P;
  }
  
  const P = 166500;
  const T = 333;
  const vn = 118.5;
  const Sol_DelP = 5;
  const l = 16;
  const d = 25;
  const N_holes = 165;
  
  const result = calculateDeltaP(P, T, vn, Sol_DelP, l, d, N_holes);
  console.log(result);
