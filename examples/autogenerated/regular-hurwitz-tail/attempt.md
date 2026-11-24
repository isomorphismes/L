# Program attempt for the regular Hurwitz tail

## Purpose

Exercise the checked shader backend on the local analytic extension of
the Hurwitz Euler–Maclaurin tail needed by the three Dirichlet characters.
For `delta = 0` the result is `(-log(tail), 0)`.

## Type-system sketch

The coordinate `delta` is a complex number carried by two real components.
The positive real `tail` lies between 6 and 7. This source implements the
local polynomial for `|delta * log(tail)| <= 1/8`. Every operation
on the two components is pure. The fragment interface exposes these two
components in its first two output slots for readback.

`ShaderFloatTuple 2` is the checked backend's two-component interface, and
its source-level `Double` primitive lowers to a fragment `highp float`. The
compiler must preserve the width and the multiplication's complex semantics.
The polynomial extends continuously to the origin and commutes with complex
conjugation. Qualification of the complete evaluator additionally needs a
valid-input check and the outer-domain quotient.

## Idriç attempt

Source is `RegularHurwitzTail.idric`. The current GLSL ES backend revision
`93308d288ce9957d03591c3afc60f0c15c8c3ec7` declares the shader tuple and
operations used here. Its own workflow pins the Idriç compiler API at
`2734ac5a86610eddfab73b1644d72b4ac85a65ee`. To try the source, place it at
`src/Example/RegularHurwitzTail.idric` in the backend checkout and run the
built `idris2-glsles --cg glsles --source-dir src --output-dir generated
src/Example/RegularHurwitzTail.idric -o regular-hurwitz-tail` there.

## Result and remaining work

The pinned backend parsed and checked this source, emitted GLSL, and
validated and linked it at L commit
`e7d1837d7eb7e3592d19f4e7e7b327ec4d1c6e1b`. The older backend pin in L
`7876b79058d6f59711151a21697369c1cf72df2f` failed upstream of source
compilation with `Undefined name ANFDef` when paired with Idriç
`94dfd99bd3e376507fedc8611053b7173b2519f0`. The compiler API uses
`Administrative_Normal_Form_Definition` at that revision. The newer backend
source uses the new name. The L selector source is a separate path.

The GLES readback step checks the generated value at zero and at both signs
of a small nonzero imaginary coordinate. The emitted origin must be real,
finite and agree with `-log(tail)` under the declared binary32 budget. The
emitted pair must be conjugate.

The packaged handwritten GLSL and its numeric probe are exercised separately
through GLES. This provides runtime approximation evidence for that shader.

[Previous checked shader backend build failure](https://github.com/isomorphismes/L/actions/runs/42005382392/job/127170681098)

[Checked regular-tail compilation and GLSL link](https://github.com/isomorphismes/L/actions/runs/45471075773/job/138718861354)
